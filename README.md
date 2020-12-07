Test principles first

Mount actually executes the html, css and js code like a browser would, but does so in a simulated way. It is “headless” , doesnt paint the screen.
Mount/render is typically used for integration testing and shallow is used for unit testing.
Sinon, mocha, chai: A lot of the functionality offered by sinon is available by default with jest so you dont need sinon. Mocha and chai are a replacement for jest.

jest.config.js - locates outside of src folder
module.exports = {
verbose: true,
setupFilesAfterEnv: ["./src/setupTest.js"],
};
setupTest.js - locates inside of src folder
import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";
configure({ adapter: new Adapter() });

You can definitely unit test react components without shallow rendering with Jest mocking to mock out the component you dont want to test.
Things which users of your component cannot do, so your tests shouldn't do them either:

- Include the ability to find by a component class or even its displayName (again, the user does not care what your component is called and neither should your test). Note: React Testing Library supports querying for elements in ways that encourage accessibility in your components and more maintainable tests.
- Getting a component instance (like enzyme's instance)
- Getting and setting a component's props (props())
- Getting and setting a component's state (state())
  I very rarely use snapshot testing with react and I certainly wouldn't use it with shallow. That's a recipe for implementation details. The whole snapshot is nothing but implementation details.
  To test our state notice we are not using any function names or the names of our state variables. We are keeping with our guiding principle and not testing implementation details. Since a user will see the text (state.text) on the UI, this is how we will query the DOM nodes. We will also query the button (button label or children) this way and click it. Finally we will query the final state based on the text as well.

Testing actions and reducers is one unit test that is always necessary, one exception to the testing implementation details rule.

function arrayify(maybeArray) {
  if (Array.isArray(maybeArray)) {
    return maybeArray
  } else if (!maybeArray) {
    return []
  } else {
    return [maybeArray]
  }
}

- Use cases a function supports is the most important consideration we keep in mind as we write tests.
- Key takeaway: Test use cases, not code.
  Here are a few aspects of React that people often think about testing which results in implementation details tests. For all of these, rather than thinking about the code, think about the observable effect that code has for the end user and developer user, that's your use case, test that.
- Lifecycle methods
- Element event handlers
- Internal Component State
  Conversely, here are things that you should be testing because they concern your two users. Each of these could change the DOM, make HTTP requests, call a callback prop, or perform any other number of observable side effects which would be useful to test:
- User interactions (using fireEvent from React Testing Library): Is the end user able to interact with the elements that the component renders?
- Changing props (using rerender from React Testing Library): What happens when the developer user re-renders your component with new props?
- Context changes (using rerender from React Testing Library): What happens when the developer user changes context resulting in your component re-rendering?
- Subscription changes: What happens when an event emitter the component subscribes to changes? (Like firebase, a redux store, a router, a media query, or DOM-based subscriptions like online status)
  Where to start?: What would be the worst thing to break in this app?
  I'd suggest making a list of features that your application supports and prioritize them based on this criteria.
  Once you have a few E2E tests in place, then you can start looking at writing some integration tests for some of the edge cases that you are missing in your E2E tests and unit tests for the more complex business logic that those features are using. From here it just becomes a matter of adding tests over time.
  blog
  personal
