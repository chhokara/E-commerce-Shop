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

# Security
JSON Web Tokens are being used to authorize route access. Only users who are registered and currently logged in to the system will be allowed to access certain routes in the application. 

![JWT_tokens_EN](https://user-images.githubusercontent.com/44816758/150661160-3b55a28d-e1cf-4c76-b2d2-8d47ca8b075c.png)

```javascript
const protect = asyncHandler(async (req, res, next) => {
  let token;
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith("Bearer")
  ) {
    try {
      token = req.headers.authorization.split(" ")[1];

      const decoded = jwt.verify(token, process.env.JWT_SECRET);

      req.user = await User.findById(decoded.id).select("-password");

      next();
    } catch (error) {
      console.error(error);
      res.status(401);
      throw new Error("Not authorized, token failed");
    }
  }

  if (!token) {
    res.status(401);
    throw new Error("Not authorized, no token");
  }
});
```

```javascript
export const getUserDetails = (id) => async (dispatch, getState) => {
  try {
    ...
    const config = {
      headers: {
        Authorization: `Bearer ${userInfo.token}`,
      },
    };

    const { data } = await axios.get(`/api/users/${id}`, config);

    dispatch({
      type: USER_DETAILS_SUCCESS,
      payload: data,
    });
  } catch (error) {
    dispatch({
      type: USER_DETAILS_FAIL,
      payload:
        error.response && error.response.data.message
          ? error.response.data.message
          : error.message,
    });
  }
};
```
