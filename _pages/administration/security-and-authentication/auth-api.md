---
UID: 56f984458bcec
post_title: Authentication HTTP API Endpoint
post_excerpt: ""
layout: page
published: true
menu_order: 105
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
You can make external calls to HTTP API endpoints in your DCOS cluster.

You must first obtain an authentication token and then include it in your HTTP request. 

By default authentication tokens expire after 5 days. You can view the expiration time in the ["exp" (Expiration Time) Claim](https://tools.ietf.org/html/rfc7519#section-4.1.4) of the JSON Web Token (JWT). You can refresh your JWT by re-logging in to DCOS.

# Generate the authentication token

### Request

Log in with a POST request against `acs/api/v1/auth/login`.

### Data

Specify the user ID (`<user-id>`), password (`<password>`), and external hostname (`<master-host-name>`):

    $ curl --data '{"uid":"<user-id>", "password":"<your-password>"}' \
        --header "Content-Type:application/json" \
        http://<master-host-name>/acs/api/v1/auth/login
    

### Response

The response provides an authentication token that you can provide in the HTTP API `Authorization` header for subsequent requests:

    {
    "token": "<authtoken>"
    }
    

# Make HTTP request using the Authorization header

To authenticate an HTTP request against a DCOS component, specify the `<authtoken>` in the request header.

For example, to access the Marathon API:

    $ curl --header "Authorization: token=<authtoken>" http://<master-host-name>/service/marathon/v2/apps
    

For example, to access the Mesos API:

    $ curl --header "Authorization: token=<authtoken>" http://<master-host-name>/mesos/master/state.json
    
# Get the auth access log

Issue this command from your master node:

    GET /mesos/master/state-summary HTTP/1.1