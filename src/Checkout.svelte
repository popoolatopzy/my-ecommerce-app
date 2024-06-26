<!-- Checkout.svelte -->
<script>
    import { onMount } from 'svelte';
    import { navigate } from 'svelte-routing';
    import { Link } from 'svelte-routing';
    import Navbar from './Navbar.svelte';
    
    function getCookie(name) {
        const value = `; ${document.cookie}`;
        const parts = value.split(`; ${name}=`);
        if (parts.length === 2) return parts.pop().split(';').shift();
    }

    let userId = getCookie('userId');
    let cart = [];

    const fetchCartItems = async () => {
        try {
            const cartResponse = await fetch(`http://localhost:1337/api/cart-items`);
            const cartData = await cartResponse.json();
            const userCartItems = cartData.data.filter(item => item.attributes.userID === userId);
            const productPromises = userCartItems.map(item => 
                fetch(`http://localhost:1337/api/products/${item.attributes.productID}?populate=*`).then(response => response.json())
            );
            
            const products = await Promise.all(productPromises);
            cart = products.map(productData => {
                const attributes = productData.data.attributes;
                const imageUrl = attributes.productImage.data[0].attributes.url;
                return {
                    id: productData.data.id,
                    name: attributes.productName,
                    description: attributes.productDescription,
                    price: parseFloat(attributes.productPrice),
                    imageUrl: `http://localhost:1337${imageUrl}`
                };
            });
        } catch (error) {
            console.error('Error fetching cart items:', error);
        }
    };

    onMount(() => {
        if (!userId) {
            navigate('/login');
        } else {
            fetchCartItems();
        }
    });

    const handlePurchase = () => {
        alert('Purchased items in the cart');
        navigate('/');
    };

    $: totalPrice = cart.reduce((total, product) => total + product.price, 0).toFixed(2);
</script>

<Navbar/>
<main>
    {#if cart.length > 0}
        <div class="checkout-container">
            <h1>Checkout</h1>
            <ul class="product-list">
                {#each cart as product}
                    <li class="product-item">
                        <img src={product.imageUrl} alt={product.name} />
                        <div class="product-details">
                            <p><strong>{product.name}</strong></p>
                            <p>{product.description}</p>
                            <p>Price: ${product.price.toFixed(2)}</p>
                        </div>
                    </li>
                {/each}
            </ul>
            <div class="total-price">
                <p><strong>Total Price: ${totalPrice}</strong></p>
            </div>
            <button on:click={handlePurchase}>Complete Purchase</button>
            <p><Link to="/">Back to Home</Link></p>
        </div>
    {:else}
        <p>Your cart is empty</p>
    {/if}
</main>

<style>
    html, body {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100%;
        overflow-x: hidden;
        font-family: Arial, sans-serif;
    }

    body {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }

    main {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        padding: 2em;
        background-color: #f9f9f9;
    }

    .checkout-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        background: #fff;
        border: 1px solid #ddd;
        border-radius: 10px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        padding: 2em;
        max-width: 600px;
        width: 100%;
        box-sizing: border-box;
    }

    .checkout-container h1 {
        color: #ff3e00;
        text-transform: uppercase;
        font-size: 2em;
        font-weight: 300;
        margin: 0 0 1em;
    }

    .product-list {
        list-style: none;
        padding: 0;
        margin: 0 0 2em;
        width: 100%;
    }

    .product-item {
        display: flex;
        align-items: center;
        border-bottom: 1px solid #ddd;
        padding: 1em 0;
    }

    .product-item img {
        width: 150px;
        height: auto;
        margin-right: 1em;
        border-radius: 5px;
    }

    .product-details {
        flex-grow: 1;
    }

    .product-details p {
        margin: 0.5em 0;
        font-size: 1em;
        color: #666;
    }

    .total-price {
        font-size: 1.2em;
        font-weight: bold;
        margin-bottom: 1em;
    }

    button {
        background-color: #ff3e00;
        color: white;
        border: none;
        padding: 0.75em 1.5em;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1em;
        transition: background-color 0.3s;
        margin-bottom: 1em;
    }

    button:hover {
        background-color: #e03500;
    }

    a {
        color: #ff3e00;
        text-decoration: none;
        font-size: 1em;
    }

    a:hover {
        text-decoration: underline;
    }
</style>
