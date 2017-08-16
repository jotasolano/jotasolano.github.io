# DHIS2 Clinical and Genomic Data Visualization Web app


## What is needed to run this visualization

* A DHIS2 instance, either installed locally or in the cloud
* Credentials (username and password) for the aforementioned instance
* Node.js installed (v 7.10 or higher)
* NPM installed


## Installing DHIS2

There's several ways to install DHIS2. For testing purposes you might want to install locally. The easiest way is to use the Docker version of DHIS2, available [in this link](https://github.com/pgracio/dhis2-docker). This docker image will use the latest DHIS2 WAR image available on the computer. For first time installation, follow the instructions on that repo.

Because DHIS2 constanly releases new images based on bug fixes, you might want to donwload a more recent docker image of the DHIS2 WAR file. These images can be found [in this link](https://hub.docker.com/r/dhis2/dhis2-web/tags/). Once downloaded, DHIS2 docker will simply load the most recent image. Note that downloading an image will not affect your database.


## Accessing program data on DHIS2

This web app makes certain assumptions about your DHIS2 program data. This is because all DHIS2 instances will have different Unique Identifiers for every Data Element, and therefore when calling data on the front end, the only way to ensure scalability and replication was to use the "Name" of the Data Element. For instance, in instance A the unique identifier for the data element "location" could be "F3ogKBuviRA", whereas in instance B the same data element could have a unique identifier "NczRMzhFDdO". When the back end of this application requests program data, it will also get the Display Values of all the unique identifiers contained in the program, using the internal API endpoint `/api/dhis/dataElements/[unique IDs]` and will use those display names as object keys to access data for the visualizations.

> A configuration file may be created in the future so that users can further customize the display names according to their specific DHIS2 instance.

### Assumptions

Currently, the app will assume that your data is **Program Data from a single program** and that at least the following data elements are included:

| Data Element Type | Display Name  		 		| Example      |
| ----------------- | --------------------- | ------------ |
| Coordinate 				| Household location 		| [-10.6,7.81] |
| String 						| GenBank IDs 			 		| "KR105312.1" |
| String 						| Kenema &#124; Outcome | "Died"			 |


> Note that the app also uses the "eventDate" property of every event to create the timeline with all the cases.

Any other clinical or genomic medatada contained in the payload will also be parsed as a string, but it is currently not used in the application.


## Local configurations

Before you run the application, make sure these configurations have been checked:

### Server and program settings

* If you're using this repo to visualize data from another program, then you must change the program identifier, as well the access credentials. To do so, navigate to `./controllers/appController.js`

There you can modify the `qd` object, specifically the `programid` property, with the unique identifier of your program. To find the unique identifier of your program:

```
let qd = {
	program: '[Name of your program]',
	programid: '[Unique identifier of your program]', //<- Change this
	orgunit: [This property is not currently used],
}
```

> In the future, an interface to select a program from a list of all DHIS programs available may be implemented.

* Additionaly, you may need to change the url of the API endpoints, since it's currently `http://localhost:8085/...`, which points to a local Docker DHIS2 instance.

* Finally, make sure your credentials are correct. Currently the credentials are *admin* and *district* for username and password respectively.


### Mapbox Token:

This visualization uses Mapbox layers. You can get a free token by registering at Mapbox. Create a file on the root of the project called `Secret.js`, with the following code:

```
const Secret = {
	mapbox: [Your token goes here]
}

export default Secret;
```


## Running this repo

Once your DHIS2 instance is running and you have access credentials, you may start the app project using `npm run dev` as detailed in the repo's instructions. 
