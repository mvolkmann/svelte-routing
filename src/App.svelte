<script>
  import page from 'page';
  import qs from 'query-string';

  // This is the "not found" page component.
  import NotFound from './NotFound.svelte';

  // These are the page components.
  import Page1 from './Page1.svelte';
  import Page2 from './Page2.svelte';
  import Page3 from './Page3.svelte';

  // This holds the page component to be rendered.
  let component;

  // This holds all the props to be passed to the page component.
  let props = {};

  // This is a middleware that parses query strings
  // and puts the result back on the context object.
  function parseQueryString(context, next) {
    context.query = qs.parse(context.querystring);
    props = {}; // clears previous value
    next(); // allows next middleware to run
  }

  // This causes the parseQueryString middleware
  // to run on every path.
  // If the first parameter, '*', is omitted,
  // it will do the same thing.
  page('*', parseQueryString);

  // This is the root path for the app.
  page('/', context => {
    component = Page1;
    props = {p1: 'alpha', q1: 'beta'};
  });

  // This path requires one path parameter, p1.
  // A second path parameter, p2, is optional.
  // This is indicated by following the
  // path parameter name with a question mark.
  page('/one/:p1/:p2?', context => {
    component = Page1;
    const {params, query} = context;
    // These are passed to the component to be rendered
    // in <svelte:component> later.
    props = {...params, ...query};
  });

  // This path doesn't use any path or query parameters.
  page('/two', () => component = Page2);

  // This path doesn't use any path or query parameters.
  page('/three', () => component = Page3);

  // Since this is the last registered path
  // and it matches everything
  // it will be invoked for any path not already handled.
  // If the first parameter, '*', is omitted,
  // it will do the same thing.
  page('*', context => {
    console.log('App.svelte x: context =', context);
    component = NotFound;
    props = {path: context.path};
  });

  // This tells "page" to start
  page.start();
</script>

<style>
  :global(body) {
    padding: 0;
  }

  :global(h1) {
    margin-top: 0;
  }

  :root {
    --space: 20px;
  }

  a {
    --padding: 5px;
    background-color: white;
    border: solid gray 1px;
    border-radius: var(--padding);
    color: black;
    display: inline-block;
    margin-right: calc(var(--space) / 2);
    padding: var(--padding);
    text-decoration: none;
  }

  button {
    margin-bottom: 0;
    margin-top: var(--space);
  }

  main {
    padding: var(--space);
  }

  nav {
    background-color: cornflowerblue;
    padding: var(--space);
  }
</style>

<nav>
  <!-- Note how this link uses different path
       and query parameters than the "/" path. -->
  <a href="/one/v1/v2?q1=v3&q2=v4">One</a>
  <a href="/two">Two</a>
  <a href="/three">Three</a>
</nav>

<main>
  <svelte:component this={component} {...props} />

  <!-- This demonstrates programmatic navigation. -->
  <button on:click={() => page.show('/two')}>Go To Page Two</button>
</main>
