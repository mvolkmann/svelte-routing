<script>
  export let p1; // required
  export let p2 = undefined;
  export let q1; // required
  export let q2 = undefined;

  // This shows that we can access
  // all the props in a single object.
  console.log('Page1 $$props =', $$props);
</script>

<h1>Page 1</h1>

<div>p1 = {p1}</div>
<div>p2 = {p2}</div>
<div>q1 = {q1}</div>
<div>q2 = {q2}</div>
