# Node.js Middleware for Shopify Integration

This project implements a secure Node.js + Express middleware to connect with the Shopify Admin API. It handles authentication, fetches product data using GraphQL, and stores the results locally.

## ✅ Features Implemented

### Core Requirements
- [x] **Express App Setup:** Basic server running on port 3000.
- [x] **Custom Middleware:** Authenticates credentials from `.env`, logs every incoming request, and prepares secure API configurations for reuse.
- [x] **Product Sync Endpoint:** `/sync-products` endpoint to fetch data securely.
- [x] **Error Handling:** Wrapped in `try/catch` blocks with async/await for graceful failure management.

### ⭐ Bonus Features (Completed)
- [x] **Local Storage:** Fetched product data is automatically saved to a local file (`synced_products.json`).
- [x] **Rate Limiting:** Implemented `express-rate-limit` to restrict traffic (50 requests per 15 minutes) to prevent abuse.
- [x] **GraphQL API:** Used Shopify's GraphQL Admin API instead of REST for more efficient, precise data fetching.

---

### 📂 How to Run the Project


The complete source code is compressed in the ZIP file included in this repository.

**Step 1: Unzip and Ope**n

1. Download and extract the ZIP file.

2. Open the extracted folder in VS Code.

**Step 2: Setup**


Open the terminal in VS Code and run the following command to install the necessary dependencies:


	npm install

**Step 3: Configure Credentials**

1. Create a new file named .env in the root folder.

2. Add your Shopify store credentials inside it (format below):

  PORT=3000<br>
	SHOPIFY_STORE_DOMAIN=your-shop-name.myshopify.com<br>
	SHOPIFY_ADMIN_API_TOKEN=shpat_YOUR_ACCESS_TOKEN


(Note: Do not add https:// to the domain)


These are my store credentials: <br>
**SHOPIFY_STORE_DOMAIN = stylish-stitches-utsav.myshopify.com**

**SHOPIFY_ADMIN_API_TOKEN=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**

**Step 4: Start the Server**


Run the server with:


	node server.js

You will see a message: 🚀 Server running on http://localhost:3000

**Step 5: Test the Functionality**


Open your browser and visit this URL:

http://localhost:3000/sync-products

You will see a JSON response confirming the success, and a new file named synced_products.json will be created in the folder automatically.

<img width="631" height="770" alt="image" src="https://github.com/user-attachments/assets/22402966-f853-4187-9675-dd1f67e29729" />



## 📡 API Usage Details

### Endpoint: Sync Products
**URL:** `GET http://localhost:3000/sync-products`

**How to Trigger:**
Open your web browser or an API tool (like Postman) and navigate to the URL above.

**Process Flow:**
1.  **Authentication:** The `shopifyAuthMiddleware` verifies that API credentials exist in the environment variables.
2.  **Logging:** The server logs the timestamp and request type.
3.  **Fetching:** The server sends a **GraphQL** query to Shopify to fetch specific fields (ID, Title, Status, Image, Price) for the first 10 products.
4.  **Storage:** The fetched data is written to a local file named `synced_products.json`.
5.  **Response:** The server returns a JSON object to the client with the status and data.

**Sample JSON Response:**
```json
{
  "success": true,
  "message": "Products synced successfully",
  "count": 3,
  "data": [
    {
      "id": "gid://shopify/Product/8010693115997",
      "title": "Test-Product-1",
      "handle": "test-product-1",
      "status": "ACTIVE",
      "featuredImage": null,
      "priceRangeV2": {
        "minVariantPrice": {
          "amount": "0.0",
          "currencyCode": "USD"
        }
      }
    },
    
   
  ]
}
```

---

## 🧠 Middleware Logic Explanation

The core of this application is the `shopifyAuthMiddleware` function found in `server.js`.

1.  **Security Check:** It acts as a gatekeeper before the main logic runs. It checks if `SHOPIFY_ADMIN_API_TOKEN` is present. If not, it blocks the request immediately with a 500 error.
2.  **Request logging:** It uses `console.log` to track every incoming request method and URL for debugging purposes.
3.  **Config Injection:** Instead of hardcoding headers in every route, the middleware constructs the `axios` configuration object (URL and Headers) and attaches it to the `req` object. This makes the code modular. Future routes can simply use `req.shopifyConfig` without needing to know the token details.
