# Cypress

Cypress is a next generation front end testing tool built for the modern web. We address the key pain points developers and QA engineers face when testing modern applications.

We make it possible to:

- Set up tests
- Write tests
- Run tests
- Debug Tests

## Who uses Cypress?

Cypress enables you to write all types of tests:

- End-to-end tests
- Component tests
- Integration tests
- Unit tests

## Cypress ecosystem

Cypress consists of a free, open source, locally installed application and Cypress Cloud for recording your tests.

- **First**: Cypress helps you set up and start writing tests every day while you build your application locally. TDD at its best!
- **Later**: After building up a suite of tests and integrating Cypress with your CI Provider, Cypress Cloud can record your test runs. You'll never have to wonder: Why did this fail?

## Features

Cypress comes fully baked, batteries included. Here is a list of things it can do that no other testing framework can:

- **Time Travel**: `Cypress takes snapshots` as your tests run. Hover over commands in the Command Log to see exactly what happened at each step.
- **Debuggability**: Stop guessing why your tests are failing. Debug directly from familiar tools like Developer Tools. Our readable errors and stack traces make debugging lightning fast.
- **Automatic Waiting**: Never add waits or sleeps to your tests. Cypress automatically waits for commands and assertions before moving on. No more async hell.
- **Spies, Stubs, and Clocks**: Verify and control the behavior of functions, server responses, or timers. The same functionality you love from unit testing is right at your fingertips.
- **Network Traffic Control**: Easily control, stub, and test edge cases without involving your server. You can stub network traffic however you like.
- **Consistent Results**: Our architecture doesn't use Selenium or WebDriver. Say hello to fast, consistent and reliable tests that are flake-free.
- **Screenshots and Videos**: View screenshots taken automatically on failure, or videos of your entire test suite when run from the CLI. Record to Cypress Cloud to store them with your test results for zero-configuration debugging.
- **Cross browser Testing**: Run tests within Firefox and Chrome-family browsers (including Edge and Electron) locally and optimally in a Continuous Integration pipeline.
- **Smart Orchestration**: Once you're set up to record to Cypress Cloud, easily parallelize your test suite, rerun failed specs first with Spec Prioritization, and cancel test runs on failures with Auto Cancellation for tight feedback loops.
- **Flake Detection**: Discover and diagnose unreliable tests with Cypress Cloud's Flaky test management.

## Setting up tests

```bash
$ npm install cypress --save-dev
```

```bash
$ ./node_modules/.bin/cypress open
```

## Writing tests

Tests written in Cypress are meant to be easy to read and understand. Our API comes fully baked, on top of tools you are familiar with already.

```js
describe("TodoMVC", function () {
  beforeEach(function () {
    cy.visit("http://localhost:8888");

    cy.get(".new-todo")
      .type("buy some cheese {enter}")
      .type("feed the cat {enter}")
      .type("book a doctors appointment {enter}");
  });

  it.only('hides "Clear Completed" with nothing checked', function () {
    cy.get(".todo-list li").eq(1).find(".toggle").check();
    cy.get(".clear-completed").should("be.visible").click();
    cy.get(".clear-completed").should("not.exist");
  });
});
```

## Test types

Cypress can be used to write several different types of tests. This can provide even more confidence that your application under test is working as intended.

### End-to-end

Cypress was originally designed to run end-to-end (E2E) tests on anything that runs in a browser. A typical E2E test visits the application in a browser and performs actions via the UI just like a real user would.

```js
it("adds todos", () => {
  cy.visit("https://todo.app.com");
  cy.get('[data-testid="new-todo"]')
    .type("write code{enter}")
    .type("write tests{enter}");
  // confirm the application is showing two items
  cy.get('[data-testid="todos"]').should("have.length", 2);
});
```

### Component

You can also use Cypress to mount components from supported web frameworks and execute component tests.

```js
import TodoList from "./components/TodoList";

it("contains the correct number of todos", () => {
  const todos = [
    { text: "Buy milk", id: 1 },
    { text: "Learn Component Testing", id: 2 },
  ];

  cy.mount(<TodoList todos={todos} />);
  // the component starts running like a mini web app
  cy.get('[data-testid="todos"]').should("have.length", todos.length);
});
```

### API

Cypress can perform arbitrary HTTP calls, thus you can use it for API testing.

```js
it("adds a todo", () => {
  cy.request({
    url: "/todos",
    method: "POST",
    body: {
      title: "Write REST API",
    },
  })
    .its("body")
    .should("deep.contain", {
      title: "Write REST API",
      completed: false,
    });
});
```

### Other

Finally, through a large number of official and 3rd party plugins you can write Cypress a11y, visual, email and other types of tests.

![Cypress in the real world](https://docs.cypress.io/img/guides/overview/v10/real-world-app.png)

<br />
<br />

---

## Architecture

Most testing tools (like Selenium) operate by running outside of the browser and executing remote commands across the network. Cypress is the exact opposite. Cypress is executed in the same run loop as your application.

Behind Cypress is a Node server process. Cypress and the Node process constantly communicate, synchronize, and perform tasks on behalf of each other. Having access to both parts (front and back) gives us the ability to respond to your application's events in real time, while at the same time work outside of the browser for tasks that require a higher privilege.

Cypress also operates at the network layer by reading and altering web traffic on the fly. This enables Cypress to not only modify everything coming in and out of the browser, but also to change code that may interfere with its ability to automate the browser.

Cypress ultimately controls the entire automation process from top to bottom, which puts it in the unique position of being able to understand everything happening in and outside of the browser. This means Cypress is capable of delivering more consistent results than any other testing tool.

Because Cypress is installed locally on your machine, it can additionally tap into the operating system for automation tasks. This makes performing tasks such as taking screenshots, recording videos, general file system operations and network operations possible.

<br />

## Native access

Because Cypress operates within your application, that means it has native access to every single object. Whether it is the `window`, the `document`, a DOM element, your application instance, a function, a timer, a service worker, or anything else - you have access to it in your Cypress tests. There is no object serialization, there is no over-the-wire protocol - you have access to everything. Your test code can access all the same objects that your application code can.

<br />

## New kind of testing

Having ultimate control over your application, the network traffic, and native access to every host object unlocks a new way of testing that has never been possible before. Instead of being 'locked out' of your application and not being able to easily control it - Cypress instead lets you alter any aspect of how your application works. Instead of slow and expensive tests, such as creating the state required for a given situation, you can create these states artificially like you would in an unit test. For instance you can:

- Stub the browser or your application's functions and force them to behave as needed in your test case.
- Expose data stores (like in Redux) so you can programmatically alter the state of your application directly from your test code.
- Test edge cases like 'empty views' by forcing your server to send empty responses.
- Test how your application responds to errors on your server by modifying response status codes to be 500.
- Modify DOM elements directly - like forcing hidden elements to be shown.
- Use 3rd party plugins programmatically. Instead of fussing with complex UI widgets like multi selects, autocompletes, drop downs, tree views or calendars, you can call methods directly from your test code to control them.
- Prevent Google Analytics from loading before any of your application code executes when testing.
- Get synchronous notifications whenever your application transitions to a new page or when it begins to unload.
- Control time by moving forward or backward so that timers or polls automatically fire without having to wait for the required time in your tests.
- Add your own event listeners to respond to your application. You could update your application code to behave differently when under tests in Cypress. You can control WebSocket messages from within Cypress, conditionally load 3rd party scripts, or call functions directly on your application.
