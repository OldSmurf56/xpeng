# Guide to retrieving data from Xpeng cars and publish in Home Assistant

## Overview

This is a guide and a bit of code for retrieving Xpeng data from enode.com to be able to present these in Home Assistant. This solution will retrieve all data from the enode server but please note that Xpeng currently doesn't upload all possible fields. For example, odometer data are currently not available. But maybe some day.
Also note that the cars' position is only updated when the car is switched off and not while driving or in neutral. Everything will update automatically every 5 minutes. 

### Shortcomings
I have some challenges with plotting the current position on a map - I can't get it working. The position is visible as "latitude, longitude" on the dashboard, but i can't seem to get the cars' position on a map. Any help is welcome
The current solution may look clumsy, but it works :-) Any improvements are welcome.


## Prerequisites

### Xpeng

Have your user name and password for your Xpeng account ready.

### Enode

All communication with enode.com uses Curl commands which can be issued from the command prompt within Windows - this is also quite effective for initial testing. 
Alternatively these Curl commands can also be run from Home Asistant from Terminal. 
1. You will need to create a Developer account on ENODE.COM on https://developers.enode.com/api/reference#introduction.
   The free user account will only let you access the sandbox area. Contact Sales on https://enode.com/contact and ask them if your account can be upgraded to a production account.
   With a bit of luck you will get access to a number of free devices.
2. With your Client ID you can create a Client secret according to this guide: https://developers.enode.com/api/reference#api-credentials.
   Keep your ClientID and Client Secret in a safe place - we will need this later on.
4. Next step is to retrieve an access token. To do this you have to send the following curl command to the enode server at https://oauth.production.enode.io/oauth2/token and replace ClientID:ClientSecret with your data.
      
   curl https://oauth.production.enode.io/oauth2/token -X POST -u ClientID:ClientSecret -d "grant_type=client_credentials"

   The server will reply with a token that will expire after an hour.
5. Within an hour (or you will have to update your token first) you can add your car to this Client; this is a bit tricky and also involves running Curl commands
   Open a command prompt from within Windows and run the following command and replace {YOUR_ACCESS_TOKEN} with your data.

   curl https://enode-api.production.enode.io/users/your_userID/link -X POST ^
   -H "Authorization: Bearer {YOUR_ACCESS_TOKEN}" ^
   -H "Content-Type: application/json" ^
   -d "{ \"vendorType\": \"vehicle\", \"scopes\": [\"vehicle:read:data\", \"vehicle:read:location\", \"vehicle:control:charging\"], \"language\": \"en-US\", \"redirectUri\": \"https://localhost:3000\" }"

   The above is one command! The ^ at the end of each line simply instructs the command prompt to continue input. Enode.com will reply with a link to a landing page that you can click on. The landing page will prompt you to authorize your car being added to this client. You will need your Xpeng user name and password for this!


### Home Assistant

My solution relies on a Home Assistant installation with both MQTT and Node Red, so ensure that you have both installed and configured.

## Next steps

After having done all the initial setup steps and verified that you are able to receive data from Enode.com, you can download my Node Red flow and import it in Node Red. Remember to replace the YOUR_CLIENT_ID:YOUR_CLIENT_SECRET in the "Update token" Exec Node with your data. If auto-discovery is enabled, the new Xpeng device should automatically pop up in your list of MQTT devices. All the entities should also be visible in the new MQTT device. From there on use the sensors where you need them.

# Changelog
V2: Removed QoS and Retain settings in MQTT publish




