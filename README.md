# ProShop - A Shopping Site for the Latest Tech Gadgets
ProShop is your one stop shop for purchasing state of the art tech products. Some of the core features include the ability to add items into a shopping cart, proceed
to the checkout process, and view previously purchased items.

![Screen Shot 2020-09-29 at 5 50 52 PM](https://user-images.githubusercontent.com/44816758/150244312-67d301fa-41bd-4f87-974d-cb0297aa6787.png)

# Technologies Used

Front End:
- React
- React Hooks
- Functional Components
- Redux

Back End:
- Node JS
- Express
- MongoDB
- Mongoose
- JSON Web Tokens

# Front End Architecture
The front end of this application uses React's built in functional component system and hooks to organize re-usable units of code. Redux is used to manage global level state of the 
application which includes things like registered users, products, and orders.

<img width="1016" alt="Screen Shot 2022-01-19 at 4 51 23 PM" src="https://user-images.githubusercontent.com/44816758/150243190-99aaf25a-9022-4c2d-a5f1-a556328ebbbf.png">

```javascript
const PlaceOrderScreen = ({ history }) => {
  const dispatch = useDispatch();

  const cart = useSelector((state) => state.cart);
  
  ...
  
  const placeOrderHandler = () => {
    dispatch(
      createOrder({
        orderItems: cart.cartItems,
        shippingAddress: cart.shippingAddress,
        paymentMethod: cart.paymentMethod,
        itemsPrice: cart.itemsPrice,
        taxPrice: cart.taxPrice,
        shippingPrice: cart.shippingPrice,
        totalPrice: cart.totalPrice,
      })
    );
  };
  
  ...
  
  <Button
       type="button"
       className="btn-block"
       disabled={cart.cartItems.length === 0}
       onClick={placeOrderHandler}
  >
       Place Order
  </Button>
  
  ...

}
```

# Back End Architecture
The back end of this application consists of an express server using an MVC design pattern. Models are written using the Mongoose ORM which queries a MongoDB database. This includes
operations such as retrieving users and getting shopping cart items.

<img width="1041" alt="Screen Shot 2022-01-19 at 4 52 13 PM" src="https://user-images.githubusercontent.com/44816758/150243215-8e2ed615-45c5-44ba-a173-d78a32498f40.png">

```javascript
app.use("/api/orders", orderRoutes);

...

router.route("/").post(protect, addOrderItems);
router.route("/myorders").get(protect, getMyOrders);
router.route("/:id").get(protect, getOrderById);
router.route("/:id/pay").put(protect, updateOrderToPaid);

```

```javascript
const addOrderItems = asyncHandler(async (req, res) => {
  const {
    orderItems,
    shippingAddress,
    paymentMethod,
    itemsPrice,
    taxPrice,
    shippingPrice,
    totalPrice,
  } = req.body;

  if (orderItems && orderItems.length === 0) {
    res.status(400);
    throw new Error("No order items");
  } else {
    const order = new Order({
      orderItems,
      user: req.user._id,
      shippingAddress,
      paymentMethod,
      itemsPrice,
      taxPrice,
      shippingPrice,
      totalPrice,
    });

    const createdOrder = await order.save();

    res.status(201).json(createdOrder);
  }
});
```


