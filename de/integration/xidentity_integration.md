# Integration via XignIn Identity

## Integration via OpenID Connect Federation
To Integrate XignIn Identity and XignIn, an OpenID Connect Provider (IDP) needs to be created

1. Log into the administration console
2. From the drop down on the upper left side, select the realm in which the IDP should be created
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_step_2.png" alt="drawing" width="200"/>
    </div>
   
3. Select the option Identity Providers
4. From the following screen select **OpenID Connect v1.0** in the drop-down menu
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_step_4.png" alt="drawing" />
    </div>

5. Configure the Identity Provider, these are the mandatory configuration options<br>
    a) Alias – The alias for the IdP, internally used by XignIn Identity<br>
    b) Authorization URL – The URL, XignIn Identity sends the user-agent to, to authenticate the user (e.g. https://xign.me/openid/authenticate)<br>
    c) Token URL – The URL, XignIn Identity retrieves the identity token from, after authentication<br>
    d) Client ID – The ID of the XignIn Identity client in the XignIn System (generated after registering the client with XignIn)<br>
    e) Default Scopes – The scopes (i.e. the user attributes) that should be included in the identity token (set the value to profile email)<br>


### Set as default IDP

Setting XignIn as default IdP has the advantage that unauthenticated users will be directly redirected to XignIn, otherwise the user will be presented with a list, where he has to choose from all configured IdPs.

To configure XignIn as the default IdP follow these steps: 

1.	Go to **Authentication > Flows** and make a copy of the Browser flow
2.	Open the configuration of the Identity Provider Redirector Auth Type by choosing **Actions > Config** and you will see the following UI
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_default_idp_1.png" alt="drawing" />
    </div>
3.	Under **Default Identity Provider** enter the name of the created OpenID Connect Identity Provider from the previous steps and an Alias for this configuration and save the settings
4.	If the settings are successfully saved the Identity Provider Redirector will be suffixed with the Alias given to the configuration:
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_default_idp_2.png" alt="drawing" />
    </div>


### Create User on first login

The Identity Provider can be configured to automatically create a xidentity user (if not already existing) when he first logs in with XignIn. To configure this behaviour, follow these steps:

1.	Go to **Authentication > Flows** and copy the First Broker Login flow
2.	Delete all unnecessary Auth Types as shown in the following example, set **User Creation Or Linking** as **REQUIRED** and **Create User If Unique** as **ALTERNATIVE**
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_create_user_on_login_1.png" alt="drawing" />
    </div>

3.	Set the newly created flow in the **First Login Flow** section of the IdP settings
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_create_user_on_login_2.png" alt="drawing" />
    </div>


### Account mapping

When authenticating via OpenID Connect for the first time, XignIn Identity applies following logic to the process:

KC checks whether a user exists with the same e-mail address, which is delivered in the Identity Token
1. If a user exists, he/she is prompted for his/her password to link the account to the identity provided by XignIn
2. If there is no existing user, a new user is created and registered in XignIn Identity, that is linked to the provided Identity (in this case the e-mail address will be the username of the newly created user)


### Redirect URI for XignIn
XignIn Identity has to be registered with XignIn prior to completing the configuration of the IdP. The Redirect URI, which has to be registered as well during the registration at XignIn will be of the form:

https://\<xidentity-host\>/auth/realms/\<realm-name\>/broker/oidc/endpoint

XignIn will redirect the user-agent to this URL, while appending an authorization code for retrieving the token response (i.e. the identity token)

## Integration via XignIn Authentication SPI

### Configure Authentication Flow and Authentication Provider

XignSys provides a XignIn Identity Authentication SPI to integrate XignIn with XignIn Identity. To use the plugin, you have to create an organization and a client (see 1.3.3)and accomplish the following steps:

1.	Go to Authentication > Flows and copy the Browser flow
2.	Delete the Forms node
3.	Click Add Execution and select XignIn from the Provider list and click Save 
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_plugin_1.png" alt="drawing" />
    </div>
4.	Set XignIn as REQUIRED
5.	Configure the XignIn Provider by click Actions > Config
6.	The configuration requires you to enter some information about the integrated XignIn instance and about your XignIn Identity instance
    <br>
    <br>
    <div style="text-align:center">
    <img src="../_media/xidentity_plugin_2.png" alt="drawing" />
    </div>

    * Alias – An Alias for the configuration
    * Mapping Attribute – The name of the claim of the authentication response from XignIn that maps to the xidentity username
    * Create User – create xidentity user, if not already existing
    * JSON Configuration – The contents of the downloaded configuration file, which is available after creating a client for you organization (WHEN CREATING A CLIENT THE REDIRECT URL HAS TO BE IN THE FORM OF  https://\<xidentity-url\>/auth/realms/˙<realm-name˙>/login-actions/authenticate)

After the execution is successfully configured you are able to use the authenticator. You have two options: 

1.	Activate the authenticator for the entire realm
2.	Activate the authenticator for specific clients only

### Activate for entire realm

To activate the authenticator for the entire realm you have to override the browser binding. Go to Authentication > Bindings and select the newly created flow from the Browser Flow drop-down-list and click Save.


### Activate for specific client
To activate the authenticator for a specific client only, go to Clients and open the settings for the client you want to activate the authenticator for. Scroll down to bottom and expand the section Authentication Flow Overrides and select the newly created flow from the Browser Flow drop-down-list and click Save.