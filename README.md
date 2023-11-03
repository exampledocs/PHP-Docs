# SkyAuth-PHP-Example
PHP example for the https://skyproject.cc authentication system.

## **Bugs**

If the default example not added to your software isn't functioning how it should, please report a bug here https://skyproject.cc/app/?page=support


## **`SkyAuthApp` instance definition**

Visit https://skyproject.cc/app/ and select your application, then click on the **PHP** tab

It'll provide you with the code which you should replace with in the `credentials.php` file.

```php
$SkyAuthApp = new SkyAuth\api("appNameHere", "SkyAuthOwnerIDHere");
```

## **Initialize application**

You must call this function prior to using any other SkyAuth function. Otherwise the other SkyAuth function won't work.

```php
$SkyAuthApp->init();
```

## **Display application information**

```php
$numKeys = $_SESSION["numUsers"];
$numUsers = $_SESSION["numKeys"];
$numOnlineUsers = $_SESSION["numOnlineUsers"];
$customerPanelLink = $_SESSION["customerPanelLink"];
```

## **Login with username/password**

```php
if ($SkyAuthApp->login("userNameHere", "passWordHere")) {
  // send user to dashboard or wherever you prefer
}
```

## **Register with username/password/key**

```php
if ($SkyAuthApp->register("userNameHere", "passWordHere", "licenseKeyHere")) {
  // send user to dashboard or wherever you prefer
}
```

## **Upgrade user username/key**

Used so the user can add extra time to their account by claiming new key.

> **Warning**
> No password is needed to upgrade account. So, unlike login, register, and license functions - you should **not** log user in after successful upgrade.

```php
if ($SkyAuthApp->upgrade("userNameHere", "licenseKeyHere")) {
			// don't login, upgrade function is not for authentication, it's simply for redeeming keys
      // make the user login with their username and password now.
}
```

## **Login with just license key**

Users can use this function if their license key has never been used before, and if it has been used before. So if you plan to just allow users to use keys, you can remove the login and register functions from your code.

```php
if ($SkyAuthApp->license("licenseKeyHere")) {
      // send user to dashboard or wherever you prefer
}
```

## **User Data**

Show information for current logged-in user.

```php
$username = $_SESSION["user_data"]["username"];
$subscriptions = $_SESSION["user_data"]["subscriptions"];
$subscription = $_SESSION["user_data"]["subscriptions"][0]->subscription;
$expiry = $_SESSION["user_data"]["subscriptions"][0]->expiry;
for ($i = 0; $i < count($subscriptions); $i++) {
    echo "#" . $i + 1 . " Subscription: " . $subscriptions[$i]->subscription . " - Subscription Expires: " . "<script>document.write(convertTimestamp(" . $subscriptions[$i]->expiry . "));</script>";
}
```

## **Check subscription name of user**

If you want to wall off parts of your app to only certain users, you can have multiple subscriptions with different names. Then, when you create licenses that correspond to the level of that subscription, users who use those licenses will get a subscription with the name of the subscription that corresponds to the level of the license key they used.

```php
if(findSubscription("default", $_SESSION["user_data"]["subscriptions"])) {
    // user has subscription with name "default"
}
else {
    // user does not have subscription with name "default"
}
```

## **Application variables**

A string that is kept on the server-side of SkyAuth. On the dashboard you can choose for each variable to be authenticated (only logged in users can access), or not authenticated (any user can access before login). These are global and static for all users, unlike User Variables which will be dicussed below this section.

```php
//* Get Public Variable
$var = $SkyAuthApp->var("varName");
echo "Variable Data: " . $var;
```

## **User Variables**

User variables are strings kept on the server-side of SkyAuth. They are specific to users. They can be set on Dashboard in the Users tab, via SellerAPI, or via your loader using the code below. `discord` is the user variable name you fetch the user variable by. `test#0001` is the variable data you get when fetching the user variable.

```php
//* Set Up User Variable
$SkyAuthApp->setvar("varName", "varData");
```

And here's how you fetch the user variable:

```php
//* Get User Variable
$var = $SkyAuthApp->getvar("varName");
echo "Variable Data: " . $var;
```

## **Application Logs**

Can be used to log data. Good for anti-debug alerts and maybe error debugging. If you set Discord webhook in the app settings of the Dashboard, it will send log messages to your Discord webhook rather than store them on site. It's recommended that you set Discord webhook, as logs on site are deleted 1 month after being sent.

You can use the log function before login & after login.

```php
//* Log Something to the SkyAuth webhook that you have set up on app settings
$SkyAuthApp->log("message");
```


## Server-sided webhooks

Send HTTP requests to URLs securely without leaking the URL in your application. You should definitely use if you want to send requests to SellerAPI from your application, otherwise if you don't use you'll be leaking your seller key to everyone. And then someone can mess up your application.

1st example is how to send request with no POST data. just a GET request to the URL. `7kR0UedlVI` is the webhook ID, `https://skyproject.cc/api/seller/?sellerkey=sellerkeyhere&type=black` is what you should put as the webhook endpoint on the dashboard. This is the part you don't want users to see. And then you have `&ip=1.1.1.1&hwid=abc` in your program code which will be added to the webhook endpoint on the SkyAuth server and then the request will be sent.

2nd example included post data, JSON. It's an example request to Discord webhook `7kR0UedlVI` is the webhook ID, `https://discord.com/api/webhooks/...` is the webhook endpoint.

```php
$result = $SkyAuthApp->webhook("7kR0UedlVI", "&ip=1.1.1.1&hwid=abc");
echo "<br> Result from Webhook: " . $result;

$result = $SkyAuthApp->webhook("7kR0UedlVI", "", "{\"content\": \"webhook message here\",\"embeds\": null}", "application/json"); // if Discord webhook message successful, response will be empty
echo "<br> Result from Webhook: " . $result;
```

## Ban the user

Ban the user and blacklist their HWID and IP Address.

Function only works after login.

The reason paramater will be the ban reason displayed to the user if they try to login, and visible on the SkyAuth dashboard.

```php
$SkyAuthApp->ban('Broke the rules');
```
