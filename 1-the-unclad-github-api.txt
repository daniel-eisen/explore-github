1. The Unclad Github API


**Getting started with auth
when starting with auth there are two ways to go username and password authentication and OAuth token. Both are inititiated the first time with https authentication but the OAuth is gotten once and then does not need to use https authentication again. Username and password authentication must be perfomed on each request.  Lets get a token so we can set it and forget it.

curl -u daniel-eisen -d '{"scopes":["public_repo"], "note": "A new authorization"}' https://api.github.com/authorizations
Enter host password for user 'daniel-eisen':
*resonse with an api key

**using Auth with curl (https://curl.haxx.se/docs/manpage.html)

-H, --header <header/@file>

(HTTP) Extra header to include in the request when sending HTTP to a server. You may specify any number of extra headers.

COMMAND:

curl -H "Authorization: token yourtoken" "https://api.github.com/user"

**Using curl on the command line is a quick way to hit api enpoints.

Calling:
https://api.github.com

We get a JSON Response even though we did not ask for anything specific.
We do not need to provide AUTH for this endpoint.
This endpoint is a map to all the other endpoints including query parameters.
That makes the github api a hypermedia api(Building Hypermedia APIs with HTML5 and NodeCreating Evolvable Hypermedia Applications)


{
  "current_user_url": "https://api.github.com/user",
  "current_user_authorizations_html_url": "https://github.com/settings/connections/applications{/client_id}",
  "authorizations_url": "https://api.github.com/authorizations",
  "code_search_url": "https://api.github.com/search/code?q={query}{&page,per_page,sort,order}",
  "commit_search_url": "https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}",
  "emails_url": "https://api.github.com/user/emails",
  "emojis_url": "https://api.github.com/emojis",
  "events_url": "https://api.github.com/events",

}

Accessing this info at the command line can be made simple by using jq a utility you can install with brew.
Notice that you must pass a dot to jq which means filter

Daniels-MacBook-Air:GithubAPI dan$ curl https://api.github.com | jq '.public_gists_url'
 **all the meta omitted**
 **the response**
""https://api.github.com/gists/public"

This is a reponse that tells is where to look for gists it is part of the hypermedia api

looking at how jq interacts with another endpoint  https://api.github.com/users/daniel-eisen/repos

The raw response looks something like this partial which removes most entries but reatains the structure of the response:
[
    {
      "id": 128421880,
      "name": "explore-github",
      "full_name": "daniel-eisen/explore-github",
      "owner": {
        "login": "daniel-eisen"
      },
      "private": false,
      "html_url": "https://github.com/daniel-eisen/explore-github",
      "license": {
        "key": "mit",
      },
      "forks": 0
    }
 ]
  
  Say I just want to know if this is a fork using jq: (https://stedolan.github.io/jq/)
  
  curl 'https://api.github.com/users/daniel-eisen/repos' | jq '.[0].fork'
  
  Here jq first filters into the array with '.[0]' and then filters into the object the array contains with '.fork' 
  
  ** Response Headers are important information
  * They give information about how the response came and why a body could be blank or something unexpexted
  
 ** Flags: -i or -v 
 * -v is the most verbose header (DNS lookup, ssl chain, request/response cycle etc..)
 * -i gives basic information about the response header
 
curl -i 'https://api.github.com'
HTTP/1.1 200 OK
Server: GitHub.com
Date: Fri, 06 Apr 2018 17:39:12 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 2165
Status: 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 49
X-RateLimit-Reset: 1523037740
Cache-Control: public, max-age=60, s-maxage=60
Vary: Accept
ETag: "7dc470913f1fe9bb6c7355b50a0737bc"
X-GitHub-Media-Type: github.v3; format=json
Access-Control-Expose-Headers: ETag, Link, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval
Access-Control-Allow-Origin: *
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
X-Frame-Options: deny
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
Content-Security-Policy: default-src 'none'
X-Runtime-rack: 0.009525
X-GitHub-Request-Id: 37E1:2AE9:DFC6:1D3FE:5AC7B0C0

*“GitHub API Rate Limits” - (X-RateLimit-Limit, X-RateLimit-Remaining, and X-RateLimit-Reset)

*X-GitHub-Media-Type is to help retrieve text or blob content from the API - You can specify the format you want to work with by sending an Accept header with your request.



 

