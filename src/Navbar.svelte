<script>
    import { Link } from 'svelte-routing';

    function getCookie(name) {
        const value = `; ${document.cookie}`;
        const parts = value.split(`; ${name}=`);
        if (parts.length === 2) return parts.pop().split(';').shift();
    }

    const email = getCookie('email');
    const fullname = getCookie('fullname');
    const userId = getCookie('userId');

    const handleLogout = () => {
        document.cookie = 'email=; path=/; max-age=0';
        document.cookie = 'fullname=; path=/; max-age=0';
        document.cookie = 'userId=; path=/; max-age=0';
        window.location.reload();
    };
</script>

<nav>
    <div class="nav-container">
        <div class="nav-brand">
            <Link to="/">My Store</Link>
        </div>
        <div class="nav-links">
            <Link to="/">Home</Link>
            {#if email}
                <span>Welcome, {fullname}</span>
                <Link to="/checkout/w">CheckOut</Link>
                <a href="#" on:click|preventDefault={handleLogout}>Logout</a>
            {:else}
                <Link to="/login">Login</Link>
                <Link to="/register">Register</Link>
            {/if}
        </div>
    </div>
</nav>

<style>
    nav {
        background-color: #71b3bf;
        color: white;
        padding: 1em 0;
        width: 100%;
        box-sizing: border-box;
    }

    .nav-container {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 2em;
        max-width: 1200px;
        margin: 0 auto;
    }

    .nav-brand a {
        color: white;
        text-decoration: none;
        font-size: 1.5em;
        font-weight: bold;
    }

    .nav-links {
        display: flex;
        gap: 1em;
    }

    .nav-links a, .nav-links span {
        color: white;
        text-decoration: none;
        font-size: 1em;
    }

    .nav-links a:hover {
        text-decoration: underline;
    }

    .nav-links span {
        font-size: 1em;
    }
</style>
