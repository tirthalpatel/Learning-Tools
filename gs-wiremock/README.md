# Getting Started with WireMock Tool

[WireMock](http://wiremock.org/) - WireMock is a flexible library for stubbing and mocking web services. Unlike general purpose mocking tools it works by creating an actual HTTP server that your code under test can connect to as it would a real web service. 

Best part is that, the [WireMock server can be run in its own process](http://wiremock.org/running-standalone.html), and configured via the Java API, JSON over HTTP or JSON files.

To POST data to WireMock server's REST endpoint, we can use curl or postman tool or any other choice of tool.

## Start and Stop as Standalone Server to Mock Services

* To Start, run this command from command prompt: 
	
	java -jar wiremock-1.57-standalone.jar --port 9999	

* To Stop, post a request with an empty body to this REST endpoint: 

	http://localhost:9999/__admin/shutdown

* To see what all service stubs exist on WireMock server, open admin console in browser: 

	http://localhost:9999/__admin/

## Manually Stubbing or Mocking REST services

A core feature of WireMock is the ability to return canned HTTP responses for requests matching criteria. These criteria can be defined in terms of URL, headers and body content.

* Post a stub mapping via the JSON API to this REST endpoint (not persisted, so will lose this after restarting server): http://localhost:9999/__admin/mappings/new

	{ 
		"request": { 
			"url": "/test", 
			"method": "GET" 
		}, 
		"response": { 
			"status": 200,
			"headers": {
						"Content-Type": "text/plain",
						"Cache-Control": "no-cache"
			},				
			"body": "Hello World /n" 
		}
	}
	
	or
	
	{
		"request": {
			"method": "GET",
			"url": "/body-file"
		},
		"response": {
			"status": 200,
			"bodyFileName": "path/to/filename.xml"
		}
	}

* Stub mappings which have been created can be persisted to the mappings directory via posting a request with an empty body to this REST endpoint: http://localhost:9999/__admin/mappings/save

* Open admin console in browser to validate the added stub mappings: http://localhost:9999/__admin/

* Access mock REST services in browser: http://localhost:9999/test		

* To reset all mock request url mapping and response data, just delete all files from "mappings" and "__files" folders.

## Record Real External Service and Playback

WireMock has the ability to create stub mappings by recording them while you send requests. This can be used to quickly capture a collection of responses from a real service then use them offline in your tests.

* To record stub mappings of real web service, run this command: 

	java -jar wiremock-1.57-standalone.jar --proxy-all="<real-rest-service-endpoint>" --port="9999" --record-mappings

* Access real services via WireMock server: http://localhost:9999/

* For any requested REST service request, first of all WireMock would check, if corresponding stub mapping exists in "mappings" folder or not. If existing, then don't hit real service rather serve recorded data from "mappings" and "__files" folders. If not existing, then WireMock calls external real service followed by records stub mapping followed by returns response to the client. That means, we can expect WireMock to playback all recorded stub mappings even if internet is unavailable.

* For Example, run this command to record and playback Github Developer APIs: 
	- Run WireMock server to proxy all requests of Github: java -jar wiremock-1.57-standalone.jar --proxy-all="https://api.github.com" --port="9999" --record-mappings
	- Access Github APIs via WireMock: http://localhost:9999/users/tirthalpatel
	
## Few already stubbed sample REST services 

Check files in "mappings" and "__files" folders / Open this url in browser: http://localhost:9999/__admin/
	
Open following URLs in browser to see REST service response,

* http://localhost:9999/employees/1
* http://localhost:9999/employees/2
* http://localhost:9999/employees/search?name=tirthal
* http://localhost:9999/employees/search?name=ishan
* http://localhost:9999/employees/
* http://localhost:9999/employees/1000
* http://localhost:9999/employees/search?name=invalid
* http://localhost:9999/users/tirthalpatel - This returns recorded response of Github API
		
## More Configuration Options?

* Refer http://wiremock.org/running-standalone.html. For example, --https-port enables HTTPS on the supplied port.
* Refer http://wiremock.org/simulating-faults.html to see how to stimulate faults via post request: http://localhost:9999/__admin/socket-delay  - { "milliseconds": 500 }
	
## Disclaimer

All [TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION](https://github.com/tomakehurst/wiremock/blob/master/LICENSE.txt) of the WireMock remains as per its license.