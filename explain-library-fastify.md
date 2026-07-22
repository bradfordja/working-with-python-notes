# What is Fastify?

Fastify is a high-performance web framework for Node.js used to build REST APIs, backend services, and microservices.

In that job requirement:
“Design, build, and maintain a scalable internal portal (Next.js) and new reporting features using Node.js and Fastify.”

the likely architecture is:
```sh
Next.js internal portal
        ↓ HTTP/JSON
Node.js + Fastify APIs
        ↓
Databases, reporting services, files, or other APIs
```
* Next.js builds the user interface.
* Fastify builds the backend API that supplies data and performs business logic.
* Node.js is the JavaScript runtime executing the Fastify application.

Fastify provides routing, request handling, validation, hooks, logging, plugins, and lifecycle management for Node.js services. (Fastify)

Simple Fastify Example
```py
import Fastify from "fastify";

const app = Fastify({
  logger: true
});

app.get("/api/reports", async (request, reply) => {
  const reports = [
    {
      id: 1,
      name: "Monthly Sales",
      status: "Complete"
    },
    {
      id: 2,
      name: "Inventory Summary",
      status: "Processing"
    }
  ];

  return reports;
});

const start = async () => {
  try {
    await app.listen({
      port: 3001,
      host: "0.0.0.0"
    });
  } catch (error) {
    app.log.error(error);
    process.exit(1);
  }
};

start();
```

The Next.js application might call it like this:
```py
const response = await fetch(
  "http://localhost:3001/api/reports"
);

const reports = await response.json();
```

Fastify Compared with Express
Fastify and Express serve a similar purpose, but Fastify is designed with performance, structured validation, plugins, and production logging as central features.
Feature	Fastify	Express
Purpose	Node.js web/API framework	Node.js web/API framework
Performance focus	Strong	General purpose
Schema validation	Built in through schemas	Usually added separately
Serialization	Schema-based optimization	Standard JSON serialization
Logging	Built-in logger support	Usually added with middleware
Plugin architecture	Encapsulated plugin system	Middleware-based
TypeScript support	Strong	Supported, but often more manual
Fastify is explicitly designed to minimize framework overhead and publishes reproducible benchmarks for comparison with other Node.js frameworks. Actual application performance will still depend on database access, network calls, queries, and business logic. (Fastify)

Route Parameters
A reporting endpoint might retrieve one report:
```py
app.get("/api/reports/:reportId", async (request, reply) => {
  const { reportId } = request.params;

  return {
    id: reportId,
    name: "Monthly Revenue Report",
    status: "Complete"
  };
});
```
Request:
GET /api/reports/123
Response:
```json
{
  "id": "123",
  "name": "Monthly Revenue Report",
  "status": "Complete"
}
```

Request Validation
One important Fastify feature is schema-based validation.
```py
app.post(
  "/api/reports",
  {
    schema: {
      body: {
        type: "object",
        required: ["name", "startDate", "endDate"],
        properties: {
          name: {
            type: "string",
            minLength: 2
          },
          startDate: {
            type: "string",
            format: "date"
          },
          endDate: {
            type: "string",
            format: "date"
          }
        }
      }
    }
  },
  async (request, reply) => {
    const reportRequest = request.body;

    const report = {
      id: crypto.randomUUID(),
      ...reportRequest,
      status: "Queued"
    };

    return reply.code(201).send(report);
  }
);
```
Fastify validates the incoming JSON before the route handler processes it.

This is conceptually similar to Pydantic validation in FastAPI:
```py
class ReportRequest(BaseModel):
    name: str = Field(min_length=2)
    start_date: date
    end_date: date
```

Fastify Hooks
Hooks allow you to run code at different stages of the request lifecycle.
For example, authentication:
```py
app.addHook("preHandler", async (request, reply) => {
  const authorization = request.headers.authorization;

  if (!authorization) {
    return reply.code(401).send({
      error: "Authentication required"
    });
  }
});
```

Common hook uses include:
* Authentication and authorization
* Request logging
* Correlation IDs
* Metrics
* Data transformation
* Response headers
* Audit tracking
Fastify documents hooks as part of its request lifecycle model. (Fastify)

Plugin Architecture
Fastify applications are commonly divided into plugins.
```py
async function reportRoutes(app) {
  app.get("/", async () => {
    return [
      {
        id: 1,
        name: "Revenue Report"
      }
    ];
  });

  app.get("/:id", async request => {
    return {
      id: request.params.id,
      name: "Revenue Report"
    };
  });
}
```

export default reportRoutes;
Register the plugin:
```py
import reportRoutes from "./routes/report-routes.js";

app.register(reportRoutes, {
  prefix: "/api/reports"
});
```
This produces:
GET /api/reports
GET /api/reports/:id
Plugins can also provide:
* Database connections
* Authentication services
* Caching
* Shared utilities
* External API clients
* Error handlers
Fastify has an ecosystem of plugins for capabilities such as authentication, caching, circuit breakers, and AWS Lambda integration. (Fastify)

Example Project Structure
For the job requirement, the application might be organized like this:
project/
│
├── portal/
│   ├── app/
│   ├── components/
│   ├── services/
│   └── package.json
│
└── reporting-api/
    ├── src/
    │   ├── routes/
    │   │   └── report-routes.js
    │   ├── services/
    │   │   └── report-service.js
    │   ├── repositories/
    │   │   └── report-repository.js
    │   ├── plugins/
    │   │   ├── database.js
    │   │   └── authentication.js
    │   ├── schemas/
    │   │   └── report-schema.js
    │   └── server.js
    └── package.json
A clean request flow would be:
Next.js page
    ↓
Fastify route
    ↓
Report service
    ↓
Report repository
    ↓
SQL database or reporting platform

What “Reporting Features” May Mean
In this job description, Fastify may be used to create APIs that:
* Retrieve report data
* Filter reports by date, user, region, or status
* Aggregate database results
* Generate CSV or Excel exports
* Start long-running reporting jobs
* Return report-job status
* Enforce role-based access
* Connect to data warehouses
* Stream large files
* Cache commonly requested results
For example:
app.get("/api/reports/sales", async request => {
  const { startDate, endDate, region } = request.query;

  return reportService.getSalesReport({
    startDate,
    endDate,
    region
  });
});

## Fastify vs FastAPI
The names are similar, but they are different frameworks.
Fastify	FastAPI
JavaScript/TypeScript	Python
Runs on Node.js	Runs on Python
Often uses JSON Schema	Uses Pydantic models
Node backend and microservices	Python backend and APIs
Similar space to Express	Similar space to Flask/Django REST
A useful memory rule:
Fastify → Node.js
FastAPI → Python

Why Use Fastify with Next.js?
Next.js can create its own Route Handlers and backend-for-frontend endpoints. However, a company may choose a separate Fastify service when the backend has substantial responsibilities, such as independent deployment, reporting workloads, many API consumers, database integrations, or separate scalability requirements. Next.js officially supports both Route Handlers and the backend-for-frontend pattern, so choosing Fastify generally signals a deliberate dedicated API layer rather than a technical requirement. (Next.js)

## Interview Answer
“Fastify is a high-performance Node.js framework for building REST APIs and backend services. In this role, I would expect Next.js to provide the internal portal UI while Fastify exposes the reporting APIs, handles validation, authentication, business logic, database access, logging, and integrations. Fastify is similar to Express, but it has a stronger emphasis on schema-based validation, optimized serialization, structured logging, lifecycle hooks, and an encapsulated plugin architecture. For scalable reporting features, I would keep the routes thin, place business logic in services, isolate database access in repositories, validate requests with schemas, and use background processing for long-running reports.”
