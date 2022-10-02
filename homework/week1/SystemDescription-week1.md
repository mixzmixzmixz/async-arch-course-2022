Awesome Task Exchange System (aTES) для UberPopug Inc
week1

Event Storming scheme - link: https://miro.com/app/board/uXjVPRuV8jk=/



Services(and according Bounded Contexts)

AuthService:
 * Performs Sync auth using jwt

UserService:
* Aggregate: User
* Creates/Deletes Users (Sync http calls)
* Produces UserCreated Event
* Produces UserRoleSet Event
* Subscribed to TaskClosed Event -> Produces MoneyAddedToUser Event: adds money to user balance according to task
* Subscribed to PerformerAssigned Event -> Produces MoneyWithdrawnFromUser Event: withdraws money from user balance
* Subscribed to UserDailyIncomeCalculated Event -> Produces UserBalanceSetToZero Event: sets user balance to zero if it was positive

Events:
* UserCreated(userId: UserId)
* UserRoleSet(userId: UserId, role: Role)
* MoneyAddedToUser(userId: UserId, money: BigDecimal, taskId: TaskId)
* MoneyWithdrawnFromUser(userId: UserId, money: BigDecimal, taskId: TaskId)
* UserBalanceSetToZero(userId: UserId)


TaskTrackerService:
* Aggregate: Task
* Creates Task (Sync call and validation) -> Produces TaskCreated Event
* Closes Task (Sync call, validation) -> Produces TaskClosed Event
* Aggregate: TaskAssigner (mb domain service) - manages Task assigning 
* Subscribed to TaskPriceSet -> Produces PerformerAssigned event
* Reassigns tasks on ReassignTasks command -> Produces TasksReassigningStarted, PerformerAssigned events, TasksReassigningFinished

Events:
* TaskCreated(taskId: TaskId, taskDescription: String)
* TaskClosed(taskId: TaskId, userId: UserId)
* PerformerAssigned(taskId: TaskId, userId: UserId)


PriceCalculator (3rd party or service):
* Calculates prices for task
* Subscribed to TaskCreated -> Produces TaskPriceSet

Events:
* TaskPriceSet(taskId: TaskId, price: BigDecimal, reward: BigDecimal)


ScheduleService
* performs daily routine 
* on DayFinished event (by cron inside or smth)
* Produces UserDailyOutcomeCalculated
* Subscribed on UserDailyOutcomeCalculated, Produces MoneyDepositedToUser event after sync call to 3rdParty Payment Service (if balance was positive)
* 

Events:
* UserDailyOutcomeCalculated(userId: UserId, balance: BigDecimal)
* MoneyDepositedToUser(userId: UserId, moneyDeposited: BigDecimal)

MailService:
* Subcribed to MoneyDepositedToUser, sends mail