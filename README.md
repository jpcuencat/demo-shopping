You can verify the data has been created with the following command.

```
select * from CATEGORY;
```

**Notes:**

 - The "top" categories of the product hierarchy can be retrieved using a `parent_id` of "ffdac25a-0244-4894-bb31-a0884bc82aa9".
 - Without specifying a `category_id`, all categories for the `parent_id` are returned.
 - When a category from the "bottom" of the hierarchy is queried, a populated `products` ArrayList will be returned.  From there, the returned `product_id`s can be used with the `/product` service.
 - Category navigation is achieved by using the `parent_id` and `category_id` properties returned for each category (to build the "next level" category links).
 - `/category/ffdac25a-0244-4894-bb31-a0884bc82aa9`  =>  Category[Clothing, Cups and Mugs, Tech Accessories, Wall Decor]
 - `/category/ffdac25a-0244-4894-bb31-a0884bc82aa9/18105592-77aa-4469-8556-833b419dacf4`  =>  Category[Clothing]
 - `/category/18105592-77aa-4469-8556-833b419dacf4`  =>  Category[T-Shirts, Hoodies, Jackets]
 - `/category/91455473-212e-4c6e-8bec-1da06779ae10`  =>  Category[Men's "Your Face...Autowired" T-Shirt, Men's "Go Away...Annotation" T-Shirt]
 - The featured products table is a simple way for web marketers to promote small numbers of products, and have them appear in an organized fashion on the main page.  The `feature_id` key is simply an integer, with the default being `202112` (for December, 2021).  You can (of course) use other numeric naming schemes.


## 1. Create your token

#### ✅ 1a. Create the token

Following the [Manage Application Tokens docs](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html) create a token with `Database Admnistrator` roles.

- Go the `Organization Settings`

- Go to `Token Management`

- Pick the role `Database Administrator` on the select box

- Click Generate token

**👁️ Walkthrough**

![image](data/img/astra-create-token.gif?raw=true)

This is what the token page looks like. You can now download the values as a CSV. We will need those values but you can also keep this window open for use later.

![image](data/img/astra-token.png?raw=true)

- `appToken:` We will use it as a api token Key to interact with APIs.

#### ✅ 1b. Save your token locally

To know more about roles of each token you can have a look to [this video.](https://www.youtube.com/watch?v=TUTCLsBuUd4&list=PL2g2h-wyI4SpWK1G3UaxXhzZc6aUFXbvL&index=8)

**Note: Make sure you don't close the window accidentally or otherwise - if you close this window before you copy the values, the application token is lost forever. They won't be available later for security reasons.**

We are now set with the database and credentials and will incorporate them into the application as we will see below.



## 2. Setup your application

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)]

### Know your Gitpod

Take a moment to read this entire section since it'll help you with the rest of the workshop as you'll be spending most of your time in Gitpod. If you're familiar with Gitpod, you can easily skip this entire section.

The extreme left side has the explorer view(1). The top left, middle to right is where you'll be editing files(2), etc. and the bottom left, middle to right is what we will refer to as the Gitpod terminal window(3) as shown below.

**👁️ Expected output**

![gitpod](data/img/gitpod-01-home-annotated.png?raw=true)


You can always get back to the file explorer view whenever by clicking on the hamburger menu on the top left followed by `View` and `Explorer` as shown below.

![gitpod](data/img/Filexplorer0.png?raw=true)


You can allow cutting and pasting into the window by clicking on `Allow` as shown below.

![gitpod](data/img/allow.png?raw=true)


✅ **2a: Enter the token**

To run the application you need to provide the credentials and identifier to the application. you will have to provide 4 values in total as shown below


Copy the environment sample file as below.

```
cp .env.example .env
```

Open the `.env` file as below.

```
gp open .env
```

- In Astra DB go back to home page by clicking the logo

- Select you database `demos` in the left panel and then copy values for `cloud-region` and `database-id` (clusterID) from the details page as shown below.

- *The DatabaseID is located on the home page*

![Ecom Welcome Screen](data/img/astra-config-1.png?raw=true)

- *The Database region (and keyspace) are located in the details page*

![Ecom Welcome Screen](data/img/astra-config-2.png?raw=true)

- Replace `application-token` with values shown on the Astra token screen or picking the values from the CSV token file your dowloaded before including the AstraCS: part of the token.


- *Make sure the Token looks something like (with AstraCS: preceeding `AstraCS:xxxxxxxxxxx:yyyyyyyyyyy`*

```yaml
# Copy this file to .env and fill in the appropriate values. Refer to README.md
# for instructions on where to find them.
export ASTRA_DB_ID=
export ASTRA_DB_REGION=
export ASTRA_DB_APPLICATION_TOKEN=
export ASTRA_DB_KEYSPACE=ecommerce
```

Make sure to inject the environment variables by running the following command

```
source .env
```

Verify that the environment variables are properly setup with the following command

```
env | grep -i astra
```

You should see four environment variables (not shown here).




## 3. Run Unit Tests

The application is now set you should be able to interact with your DB. Let's demonstrate some capabilities.

✅ **3a: Use CqlSession**

Interaction with Cassandra are implemented in Java through drivers and the main Class is `CqlSession`.

Higher level frameworks like Spring, Spring Data, or even quarkus will rely on this object so let's make sure it is part of your Spring context with a `@SpringBootTest`.

Let's change to the sub-directory from the terminal window as shown below.

```
cd backend
```

Let's run the first test with the following command.


```bash
mvn test -Dtest=com.datastax.tutorials.Test01_Connectivity
```

**👁️ Expected output**

```bash
[..init...]
Execute some Cql (CqlSession)
+ Your Keyspace: sag_ecommerce
+ Product Categories:
Clothing
Cups and Mugs
Tech Accessories
Wall Decor
List Databases available in your Organization (AstraClient)
+ Your OrganizationID: e195fbea-79b6-4d60-9291-063d8c9e6364
+ Your Databases:
workshops	 : id=8c98b922-aeb0-4435-a0d5-a2788e23dff8, region=eu-central-1
sample_apps	 : id=c2d6bd3d-6112-47f6-9b66-b033e6174f0e, region=us-east-1
sdk_tests	 : id=a52f5879-3476-42d2-b5c9-81b18fc6d103, region=us-east-1
metrics	 : id=d7ded041-3cfb-4dd4-9957-e20003c3ebe2, region=us-east-1
```

✅ **3b: Working With Spring Data**

Spring Data allows Mapping `Object <=> Table` based on annotation at the java bean level. Then by convention CQL query will be executed under the hood.

```bash
mvn test -Dtest=com.datastax.tutorials.Test02_SpringData
```

**👁️ Expected output**

```bash
Categories:
- Clothing with children:[T-Shirts, Hoodies, Jackets]
- Cups and Mugs with children:[Cups, Coffee Mugs, Travel Mugs]
- Tech Accessories with children:[Mousepads, Wrist Rests, Laptop Covers]
- Wall Decor with children:[Posters, Wall Art]
```

✅ **3c: Working With Rest Controller**

`TestRestTemplate` is a neat way to test a web controller. The application will start on a random port with `@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)`

```bash
mvn test -Dtest=com.datastax.tutorials.Test03_RestController
```

**👁️ Expected output**

```bash
List Categories:
Clothing
Cups and Mugs
Tech Accessories
Wall Decor
```

 Back to Table of Contents](#-table-of-contents)

## 4. Install the Backend

You can install the backend with the credentials using the following command

```
cd /workspace/workshop-ecommerce-app
mvn install -f backend/pom.xml -DskipTests
```



## 5. Start the Application

✅ **9a: Know your public URL**

The workshop application has opened with an ephemeral URL. To know the URL where your application endpoint will be exposed you can run the following command in the terminal after the build has completed. **Please note this URL and we will open this up in a new browser window if required later **.

```bash
gp url 8080
```
**👁️ Expected output**

![gitpod](data/img/gitpod-02-url.png?raw=true)

✅ **9b: Check APIs are not available (yet)**

Run the following command in the Gitpod terminal window

```
curl localhost:8080/api/v1/products/product/LS534S
```
**👁️ Expected output**

```
curl: (7) Failed to connect to localhost port 8080: Connection refused
```

Not to be overly concerned as we're going to be starting the application that will be served from the port.

✅ **5c: Start the application**

To start the application, we've provided a very simple convenience script that can be run as below.

```bash
./start.sh
```

Pay attention to popups being blocked as shown below and allow the popups.

![gitpod](data/img/PopupBlocked.png?raw=true)

You may encounter the following at different steps and although this may not be applicable right away, the steps are included **in advance** and summarized here so that you can keep an eye out for it. Different paths and different environments might be slightly different although Gitpod levels the playing field a bit.

Your e-commerce application should be up and running.

✅ **6d: Check APIs are now available**

Get back to Gitpod tab/window.

Leave the application running and open up another `shell` in the Gitpod terminal window by clicking on `+` and clicking on `bash` dropdown as shown below.

![gitpod](data/img/gitpod-newbash1.png?raw=true)

This will bring up a new `bash` shell as shown below.

![gitpod](data/img/gitpod-newbash2.png?raw=true)

Issue the following command in that shell as you did earlier.

```
curl localhost:8080/api/v1/products/product/LS534S
```

and you should see some output indicating that the API server is serving our ecommerce APIs.

**👁️ Expected output**

![gitpod](data/img/gitpod-newbash3.png?raw=true)

Try a few other APIs (**Hint: Look for the `RestController` java files in the respective sub-directories.**).

✅ **6e: OPTIONAL - Open in Gitpod preview window**

This might be useful for troubleshooting if your application does not automatically open up a browser tab.

If you want, you can run the following command to open your application in the preview window of Gitpod (it's much easier to use the app running in browser, though).

```
gp preview $(gp url 8080)
```

As indicated in the output below it's a very `Simple Browser`.

**👁️ Expected output**

![gitpod](data/img/gitpod-preview-1.png?raw=true)

If your application is running in the preview window but you have difficulty accessing it from the browser pay attention to popups being blocked by the browser as explained before.

✅ **6f: Get the Open API specification**

In the new shell window open the specification in the preview or browser with the following command

```
gp preview $(gp url 8080)/swagger-ui/index.html
```

The preview window looks like below. **It might help to close all the tabs or open this URL in a browser by clicking on the `open in browser` tab on the top right as shown**.

**👁️ Expected output**

![image](data/img/swagger2.png?raw=true)

Here's how it looks in the browser tab.

![image](data/img/swagger.png?raw=true)

This is the docs for the open APIs that enables the frontend or any other program to obtain the data and manipulate it with REST-based CRUD operations.

![image](data/img/splash.png?raw=true)


# Done?


