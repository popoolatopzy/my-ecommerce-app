<!-- App.svelte -->
<script>
    import { Router, Link, Route } from 'svelte-routing';
    import Home from './Home.svelte';
    import Login from './Login.svelte';
    import Register from './Register.svelte';
    import Product from './Product.svelte';
    import Checkout from './Checkout.svelte';
</script>

<main>
    <Router>
        <Route path="/" component={Home} />
        <Route path="login" component={Login} />
        <Route path="register" component={Register} />
        <Route path="product/:id" component={Product} />
        <Route path="checkout/:id" component={Checkout} />
    </Router>
</main>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 100%;
        margin: 0 auto;
    }
</style>
