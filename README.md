# xpeng
Guide to retrieving data from Xpeng cars and publish in Home Assistant

Prerequisites:
Xpeng
Have your user name and password for your Xpeng account ready.

Enode
All communication with enode.com uses Curl commands which can be issued from the command prompt within Windows - this is also quite effective for initial testing. 
Alternatively these Curl commands can also be run from Home Asistant from Terminal. 
1. You will need to create a Developer account on ENODE.COM on https://developers.enode.com/api/reference#introduction.
   The free user account will only let you access the sandbox area. Contact Sales on https://enode.com/contact and ask them if your account can be upgraded to a production account.
   With a bit of luck you will get access to a number of free devices.
2. With your Client ID you can create a Client secret according to this guide: https://developers.enode.com/api/reference#api-credentials.
   Keep your ClientID and Client Secret in a safe place - we will need this later on.
4. Next step is to retrieve a token. To do this you have to send the following curl command to the enode server at https://oauth.production.enode.io/oauth2/token :
   
   curl https://oauth.production.enode.io/oauth2/token -X POST -u ClientID:ClientSecret -d "grant_type=client_credentials"

   Replace ClientID:ClientSecret with your ID:Secret
   The server will reply with a token that will expire after an hour.
6. Within an hour (or you will have to update your token first) you can add your car to this Client; this is a bit tricky and also involves running Curl commands
   Open a command prompt from within Windows and run the following command:

   curl https://enode-api.production.enode.io/users/c9660583-6f93-463d-8fdf-e87ed352e22e/link -X POST ^
   -H "Authorization: Bearer your_access_token" ^
   -H "Content-Type: application/json" ^
   -d "{ \"vendorType\": \"vehicle\", \"scopes\": [\"vehicle:read:data\", \"vehicle:read:location\", \"vehicle:control:charging\"], \"language\": \"en-US\", \"redirectUri\": \"https://localhost:3000\" }"

   The above is one command! The ^ at the end of each line simply instructs the command prompt to continue input. Enode.com will reply with a link to a landing page that you can click on. The landing page will prompt you to authorize your car being added to this client. You will need your Xpeng user name and password for this!


Home Assistant
1. My solution relies on a Home Assistant installation with both MQTT and Node Red. 
The rest happens within Home Assistant.

Description:
The way clients communicate with enode.com is by running Curl commands. These can be issued from the command prompt within Windows - this is quite effective for initial testing. Alternatively these Curl commands can also be run from Home Asistant from Terminal. 
Before initiating any communcations with enode.com, an access token has to be created. This is done by sending the follwoing curl command to the enode server at https://oauth.production.enode.io/oauth2/token :
curl https://oauth.production.enode.io/oauth2/token -X POST -u clientID:ClientSecret -d "grant_type=client_credentials"
The server will reply with a token that will expire after 3600 seconds.
After that you can send a new Curl command containing the token and send it to https://enode-api.production.enode.io
curl 


