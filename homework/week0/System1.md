Awesome Task Exchange System (aTES) для UberPopug Inc


AuthService
* jwt auth
* uses SQL DB with users and rights for auth
* ApiGW interacts with it using sync rpc (http)
* Roles: Admin, Manager, Developer, Boss etc

UsersService  /user
* Requests: CRUD, uses SQL DB for it
* responsible for users rights  
* public endpoints - sync http CRUD for users
* /user/<id>/task  - uses analytical db (sync, eventual consist)

TaskTrackerService /tasks
* Requests: Create, Delete, Reassign, Close
* Sync Http Endpoints for accepting requests
* Sync request validation (json parsing, rights cheks)
* Reassigns tasks
* publishes events to the bus

AnalyticalService
* CQRS
* Query model - queries data from Analytic DB (sync)
* Command model - subscribed to BUS, updates Analytical DB


Schedule Service
* performs day-to-day tasks
* publish events to the BUS: SendMail(userId)


Mail Service
* Some wrapper around 3rd party services
* subscribed to BUS, sends emails

SQL DB
* relation DB for storing users, rights, tasks
* serves as projection 

EventStore
* Key - userId
* Events: TaskAssigned(taskId, userId)



What's unclear yet:
* Do we need SQL DB for tasks?
* If so - how to do transactions? (DB update + event publishing/processing)
* MB ES using User as Entity?

~1hr work

