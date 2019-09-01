# 1. SC_challenge TOC
  - [1 SC_challenge TOC](#1-sc_challenge-toc)
  - [1.2 Development Setup](#12-development-setup)
  - [1.3 API endpoints](#13-api-endpoints)
  - [1.4 Walkthrough sample route](#14-walkthrough-sample-route)
  - [1.5 Log](#15-log)


### 1.2. Development Setup

This service uses the following dev stack:

  - Server: [Express/Node/NPM](https://expressjs.com)
  - Testing: [Mocha](https://mochajs.org)

  - Important Libraries:
    - Assertions: [Chai](https://www.chaijs.com)
    - HTTP server mocking: [Nock](https://github.com/nock/nock)
    - Logging: [pino](https://github.com/pinojs/pino)

``` sh
# install npm dependencies
$> cd /path/to/SC_challenge
$> npm install
# start server
$> npm start
# Run some tests if you like
$> npm test
#enjoy
```
### 1.3 API endpoints
  - GET `/vehicles/:id`
    - retrieves vehicle information by ID
  - GET `/vehicles/:id/doors`
    - retrieves all vehicle's doors statuses by ID (locked/unlocked)
  - GET `/vehicles/:id/battery`
    - retieves percentage of battery remaining by ID
  - GET `/vehicles/:id/fuel`
    - retieves percentage of fuel tank remaining by ID
  - POST `/vehicles/:id/engine`
    - allows user to start start vehicle by ID

### 1.4 Walkthrough sample route
  - GET `/vehicles/1234`  to the  `SC API` invokes the `getVehicleInfoById`
  function which then makes an `Axios` request to the `GM API`, returning this data:
``` sh
{
  "service": "getVehicleInfo",
  "status": "200",
  "data": {
    "vin": {
      "type": "String",
      "value": "123123412412"
    },
    "color": {
      "type": "String",
      "value": "Metallic Silver"
    },
    "fourDoorSedan": {
      "type": "Boolean",
      "value": "True"
    },
    "twoDoorCoupe": {
      "type": "Boolean",
      "value": "False"
    },
    "driveTrain": {
      "type": "String",
      "value": "v8"
    }
  }
}
```
still inside the `getVehicleInfoById` function, this object is passed to `jsonFormatId` which
returns a specification formatted object that is then passed back up to the user:
```sh
{
  "vin": "1213231",
  "color": "Metallic Silver",
  "doorCount": 4,
  "driveTrain": "v8"
}
```
When an invalid ID is passed to the route, an error message is sent to the user
- GET `/vehicles/444`
```sh
{
noprofile: "There is no record for vehicle id: 444"
}
```
It is also logged and timestamped as an `ERROR`
```sh
[1567351682519] ERROR (40003 on Ryans-MBP): User is requesting vehicle id: 444
```
### 1.5 Log

No issues getting responses back from the GM API. Thought is to break out formatting logic functions into their own file in `utilities`.  These functions can definitely be optimized or their logic placed directly in the module that is making the Axios request.  But for the sake of modularity and readability, we will keep it like this.

`Logging` - Going to go with `pino` after running into a myriad of issues with `bunyan`. Only used one of six log levels (`ERROR`) and these were all when our requests were given invalid inputs.  There is plenty of opportunity for more logging given more time.  Also, need to revisit writing the logs to a file.

`Testing`- Had I just pinged the `GM API` for my tests, the test coverage could have been more robust as the process would have been more straightforward. But due to the fact that an external resource may require authentication, authorization or may have a rate limiting, I erred on the side of best practice to mock those third party request/responses.  After attempts with `moxios`, `sinon`, and `node-mocks-http`, I was finally able to get `nock` behaving as I intended



