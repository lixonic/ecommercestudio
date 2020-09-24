---
layout: post
title:  "Shopify Webhook listener with Node / Express.js"
date:   2020-09-21 00:26:35 +0530
categories: App Development 

---
Webhooks are a useful tool for apps that want to stay in sync with Shopify or execute code after a specific event occurs on a shop, for example, when a merchant creates a new product in the Shopify admin, or a customer places an order.

Webhooks created through the API by a Shopify App are verified by calculating a digital signature. Each webhook request includes a base64-encoded `X-Shopify-Hmac-SHA256` header, which is generated using the app's shared secret along with the data sent in the request.

#### Initialize a new node project and install `express` , `bodyParser` and `crypto`

``` 
npm init -y
npm install express body-parser crypto
```

#### Set up a minimal express server at index.js

```javascript
// Require express and body-parser
const express = require("express")
const bodyParser = require("body-parser")

// Initialize express and define a port
const app = express()
const PORT = 3000

// Tell express to use body-parser's JSON parsing
app.use(bodyParser.json())

// Start express on the defined port
app.listen(PORT, () => console.log(`🚀 Server running on port ${PORT}`))
```
#### Setup the Route for the webhook

```javascript
app.post("/webhook", (req, res) => {
  console.log(req.body) // Call your action on the request here
  res.status(200).end() // Responding is important
})
```
#### Setup a ngrok Tunnel for port 3000
```
ngrok http 3000
```

Now you can create a webhook under Notifications (Admin) and start testing locally.

#### Verify the webook

```javascript
const data = req.rawBody
  const hmacHeader  = req.get('X-Shopify-Hmac-Sha256')

  const hmac = crypto
    .createHmac('sha256', secretKey)
    .update(data, 'utf8', 'hex')
    .digest('base64');

    if (hmacHeader === hmac) {
        console.log('Phew, it came from Shopify!')
        res.sendStatus(200)
        console.log(req.body)
      }else{
        console.log('Danger! Not from Shopify!')
        res.sendStatus(403)
    }
```




#### Final Code

``` javascript

const express = require("express")
const bodyParser = require("body-parser")
const crypto = require("crypto")
const secretKey  = "my_shared_secret"
 
// Initialize express and define a port
const app = express()
const PORT = 3000
 
// Tell express to use body-parser's JSON parsing
app.use(
  bodyParser.json({
    verify: (req, res, buf) => {
      req.rawBody = buf;
    },
  })
);

app.post("/webhook", async(req, res) => {
 
  const data = await req.rawBody
  const hmacHeader  = await req.get('X-Shopify-Hmac-Sha256')

  try{
    const hmac = crypto
    .createHmac('sha256', secretKey)
    .update(data, 'utf8', 'hex')
    .digest('base64');

      if (hmacHeader === hmac) {
        console.log('Phew, it came from Shopify!')
        res.sendStatus(200)
        console.log(req.body)

      }else{
        console.log('Danger! Not from Shopify!')
        res.sendStatus(403)
      }
  }catch(error){
    console.log(error)
  }
   
})

app.listen(PORT, () => console.log(`🚀 Server running on port ${PORT}`))


```

Reference : [https://github.com/Shopify/shopify-express/blob/master/middleware/webhooks.js](https://github.com/Shopify/shopify-express/blob/master/middleware/webhooks.js)