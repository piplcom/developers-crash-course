# We ❤️ Testing!

---

# Overview
- Why testing? 🧪
- Unit tests
- Integration tests
- System tests
- Faking
    - Mocking 🤥
    - Test Containers 🐳
- TDD

---

## Typical Use case

- You are assigned to develop a new feature.
- When do we feel ok to write new code?
- In which cases we are worried to write new code?
- What will improve our confidence? <!-- .element: class="fragment" -->

----

## Popular Reason: Confidence

- Tests are our safety net! 🕸
- Quality tests represents the product's specs
- If a test fails, it indicates that we broke something
  - Not in PRODUCTION 🎉
- Easy to reproduce/eliminate bugs 🐞

---

# Typical kinds of tests

----

## Unit Tests

- Testing individual modules of the app
- Isolation (without any interaction with dependencies)
- Goal: confirm that the code is doing things right 

----

## Integration Tests

- Testing multiple modules as a groupcombined as a group
- Without any interaction with external dependencies (e.g., DB, sockets)
- Goal: check if different modules are working fine when combined

----

## System Tests

- Testing the entire system
  - Or even slice of the system
- Testing with real dependencies
- Goal: confirm that the entire system is working correctly

----

## Testing Hierarchy 
- Unit tests are easier to write and quicker to execute
- Optimize your ROI: 
  - Have as many unit tests as needed
  - Fewer integration tests
  - least number of system tests

----

## Testing Hierarchy 

<img src="https://cdn.softwaretestinghelp.com/wp-content/qa/uploads/2016/12/image-result-for-unit-testing-vs-functional-testin.png" height=10% width=80%>

----

## Other kind of tests

### Stress/ Load test
- How much pressure the application can handle
- i.e., how many requests are handled before crashing

### Performance test
- Check what is the latency/throughput of the system
- i.e., how many requests are processed per time unit

---

## Use case: Login Page
<img src="https://cdn1.vectorstock.com/i/1000x1000/41/40/orange-user-login-page-vector-5144140.jpg" height=50% width=35% >

----

## Components
- Username
- Password
- Login button

----

## Unit Tests
- Field length: username and password fields <!-- .element: class="fragment" -->
- Input values should be valid <!-- .element: class="fragment" -->
- Login button should be enabled only after valid values <!-- .element: class="fragment" -->

----

## Integration Tests
- User sees welcome message after enering valid values and clicking the button <!-- .element: class="fragment" -->
- User is navigated to the welcome page after valid entry and clicking the button <!-- .element: class="fragment" -->

---

# Mocks & Faking
## or: fake it until you make it!

----

## Why faking?

- Often, our code is depdent on external components
  - For example: DB connection, sockets, files etc.
- Our code should be loosly coupled with dependecies
- Otherwise, tests are expensive and time consuming:
  - Create a new DB instance for each test 👿
  - Wait for a scheduler to tick 👿
  - Many more examples

----

## How do we fake?

```scala
class UserRepository {
  val conn = DbConnection.create()

  def getUserByID(id: UserID): User = {
    val query = s"SELECT * FROM Users where id='$id'"
    val results = conn.execute(query)

    val user = convertResultToUser(results)

    user
  }
}
```

```scala
def displayUser(id: UserID) = {
  val users = new UserRepository()

  val user = users.getUserByID(id)
  println(s"your user is $user")
}

```

----

## Inject Dependencies

```scala
trait UserRepository {
  def getUserByID(id: UserID): User
}
```

```scala
object UserRepositoryLive extends UserRepository{
  val conn = DbConnection.create()

  def getUserByID(id: UserID): User = {
    val query = s"SELECT * FROM Users where id='$id'"
    val results = conn.execute(query)

    val user = convertResultToUser(results)

    user
  }
}
```

----

```scala
object UserRepositoryMock extends UserRepository{
  val userList: List[User] = List(
    User(id = UserId(1), name: "Matan"),
    User(id = UserId(2), name: "Diana"),
  )

  def getUserByID(id: UserID): User = {
    userList.find(_.id == id)
  }
}
```

```scala
def displayUser(id: UserID, users: UserRepository) = {
  val user = users.getUserByID(id)
  println(s"your user is $user")
}

```

----

## Test Containers

- Mocking is a popular techinque over the last decades
- However, sometimes the code behaves differently 
  - Real DB vs. in-memory mock
  - Elimination of network behavior
- Sometimes we have to artifically inject dependencies
  - Affects our API

----

# Test Containers 🐳

----

## What are test containers
- Library for running Docker containers for testing
- Instead of mocking - use real dependencies
  - wrapped in a container!
  - i.e., ElasticSearch, Kafka, MySQL, WebDriver 
  - or even your own custom container

----

## Test Containers: Pros and cons
- ✅ Pros: 
  - API is not changed
  - Same code in production and test environments
- ❌ Cons: 
  - Cannot be applied to all dependencies
  - Another layer in our code base  

---

# We ❤️ TDD

----

## 5 Steps of TDD

----

## Write a test
- Test should be written according to:
  - Documentation
  - Spec
  - Product requirements
- Tests should be clear enough (as discussed earlier)

----

## Test fail
- Test is going to fail and is this OK
  - Because no implementation supports the test
- The purpose of this step is to run tests before we do something 

----

## Write code 
- Write just the coded needed to make the test passed
- If the output is a `2`, you can return the number `2` (if it the expected result) 

---

## How to Unit Test?
- Unit tests differ from other kinds of tests
- They must be *F.I.R.S.T*
  - Fast
  - Independent
  - Repeatable
  - Self-describing
  - Timely

----

## Fast
  - Each test should take milliseconds to run
  - No network/DB connections
  - Thousands of tests
  - Mock external components

----

## Independent
  - Tests should NEVER depend on each other.
  - They can be executed in any order.

----

## Repeatable
  - Tests should run the same in any environment.
  - Must not depend on external factors.

----

## Self Describing
  - Each test returns a boolean result (pass or fail).
  - Developer should never examine output in order to understand if test worked.
  - Failing test output shoud be concise and clear.

----

## Timely
- Write unit tests before the code.
- Writing tests for an already written code is boring.

---
