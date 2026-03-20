const express = require("express");
const app = express();
const stripe = require("stripe")("YOUR_SECRET_KEY");
const cors = require("cors");

app.use(cors());
app.use(express.json());

let users = {};
let leads = [];

// STRIPE
app.post("/create-checkout-session", async (req,res)=>{
  const session = await stripe.checkout.sessions.create({
    payment_method_types:["card"],
    mode:"subscription",
    line_items:[{
      price_data:{
        currency:"usd",
        product_data:{name:"VIP Membership"},
        unit_amount:999,
        recurring:{interval:"month"}
      },
      quantity:1
    }],
    success_url:"http://localhost:3000",
    cancel_url:"http://localhost:3000"
  });

  res.json({id:session.id});
});

// USERS
app.post("/save-user",(req,res)=>{
  const {username, points} = req.body;
  users[username] = points;
  res.sendStatus(200);
});

app.get("/leaderboard",(req,res)=>{
  res.json(users);
});

// LEADS
app.post("/lead",(req,res)=>{
  const {email} = req.body;
  leads.push(email);
  console.log("Lead:", email);
  res.sendStatus(200);
});

app.listen(5000, ()=> console.log("Server running"));
