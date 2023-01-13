
<html>
<head>
</head>
<body>
<h1>In progress...</h1>
<h1>Integration of Oracle APEX and Office 365</h1>
<p>The package contains the functions and procedures needed to load messages from Microsoft Outlook into the Oracle APEX application using Microsoft Graph.
First of all, it is necessary to register the application on Azure in order to receive the corresponding codes that will later be used in the package (https://azure.microsoft.com/en-us). After we get the application (client) ID, directory (tenant) ID and client secret value, we are ready for further steps.
About Microsoft Graph:  "Microsoft Graph is the gateway to data and intelligence in Microsoft 365. It provides a unified programmability model that you can use to access the tremendous amount of data in Microsoft 365, Windows 10, and Enterprise Mobility + Security. Use the wealth of data in Microsoft Graph to build apps for organizations and consumers that interact with millions of users."</p>
<h1>General about the package</h1>
<p>The package consists of eight functions and two procedures that are explained in detail. First of all, the idea was for the client to enter data in the application such as, for example, the mail from which the messages will be read, then everything necessary to send the request: client id, tenant id, client secret value, wallet password and wallet path. All necessary values are stored in a table and called using functions to be used in procedures that send a request using apex_web_service.make_rest_request. After we retrieve the authentication token using the get_token function, we pass it to the get_messages procedure whose output parameter is in the form of a clob. We put the clob we got into the "temporary" table. The data in the table is in JSON format that needs to be parsed into another table, in this case the first table containing only JSON is called x_tmp, while the table into which we have to parse JSON is called uzk_mail_messages. The procedure that parses the JSON format and inserts it into the uzk_mail_messages table is called json_insert. </p>
<h1>Testing with POSTMAN</h1>
  <p>After we have received all the necessary parameters for making the call (client id, tenant id and secret value), before we start working on the procedures and functions, it would be good to try to get the results in the form of JSON format in POSTMAN. It's an free API platform for developers to design, build, test and iterate their APIs. 
After you download it to your computer and log in, you can try fetching an authentication token and fetching a message from an email in JSON format in it, you will have only two methods. The method for retrieving the authentication token will be POST, while the method for retrieving messages will be GET. Likewise, it is necessary to set all the parameters that I will explain in further steps. </p>
  <p>  Retrieving authentication token:  </p>
  <ol type="1">
      <li> HTTP request method: POST </li>
      <li> URL: https://login.microsoftonline.com/{your_tenant_id}/oauth2/v2.0/token </li>
      <li> Headers: KEY: 'Content-Type'  VALUE: 'application/x-www-form-urlencoded'</li>
      <li> Body: KEY: 'grant_type', 'client_id', client_secret', 'scope'  VALUE: 'client_credentials', 'your_client_id', 'your_client_secret', 'https://graph.microsoft.com/.default' </li>
    <li> Press SEND </li>
   </ol>
  <p> 
As a result, you should get token_type: Bearer and under access_token you should get a code that we will use later in retrieving messages. 
The next tab that we will add in POSTMAN will be for retrieving messages from Microsoft Outlook, steps on how to send a request:
  </p>
  <ol type="1">
      <li> HTTP request method: GET </li>
      <li> URL: https://graph.microsoft.com/v1.0/users/{your_mail}/mailFolders/Inbox/messages?$select=id,receivedDateTime,hasAttachments,subject,bodyPreview,sender,from,importance 
    (
The $select parameter determines which message data we will see in the query result, 
we can add parameters under the params menu, 
example: KEY - $select VALUE - id,receivedDateTime,hasAttachments,subject,bodyPreview,sender,from,importance)</li>
      <li> 
Under the authorization menu, our authorization type is Bearer token, and under the value we put the token we received</li>
      <li> 
Under the headers menu, we must add the following parameters: KEY - Content-Type VALUE - application/json
KEY - Prefer VALUE - outlook.body-content-type=text, KEY - Authorization VALUE - Bearer your_token </li>
    <li> Press SEND </li>
   </ol>
 <p> As a result, we received messages in JSON format. It is desirable to understand the way the request is sent in POSTMAN because we configure apex_web_service.make_rest_request in a similar way. </p>
</body>
</html>

