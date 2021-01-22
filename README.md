# opentracing-hazelcast

## Prepare the environment
In this step we will prepare the environment for the hands on steps
- Install OneAgent
- Enable OpenTelemetry instrumentation
- Clone the repos for the Java and Go exercise

### Step 1: Install OneAgent
- Access the Dynatrace tenant provided to you
- In your RDP Windows instance provided to you, download the Windows based OneAgent from Deploy Dynatrace
- Start the installation with default settings

### Step 2: Enable OpenTelemetry instrumentation
- Go to your environment, under `Settings` > `Server-side service monitoring` > `Deep Monitoring` > `OpenTelemetry and OpenTracing`)
  ![Deep Monitoring](../../assets/images/deep_monitoring.png)

### Step 3: Clone the repos
- Launch Visual Studio Code
- Click on `clone repository`, and enter `https://github.com/Dynatrace/perform-2021-hotday/tree/main/Opentelemetry_With_Dynatrace/golang`
- Open a new Window, click on `clone repository` and enter `https://github.com/Dynatrace/perform-2021-hotday/tree/main/Opentelemetry_With_Dynatrace/java`

### You've arrived
- You are now ready to start the hands on!

## OneAgent out-of-the-box instrumentation
In this step we will compile our Java Springboot application and explore the out-of-the-box instrumentation of the OneAgent

### Step 1: Compile and run the application
- Edit hazelcast-client.yaml in src > main > resources
- Input the public IP address ``
- Open up a Terminal from the top menu bar
- Run this command `mvn spring-boot:run`
- Check that the program compiles without any errors and it connects to the HazelCast nodes
- Open a new terminal tab
- Enter `curl http://localhost:8080/get?key=testkey`
- You will observe a response of `{"response":null}`. This is normal as you have not populated the HazelCast map with entries.

```powershell
PS C:\Users\dtu.training\Documents\opentracing-hazelcast> curl http://localhost:8080/get?key=testkey

StatusCode        : 200
StatusDescription :
Content           : {"response":null}
...
```
### Step 2: Populate the HazelCast instance with entries and retrieve the data
- Enter `curl http://localhost:8080/pop1`

```powershell
PS C:\Users\dtu.training\Documents\opentracing-hazelcast> curl http://localhost:8080/pop1

StatusCode        : 200
StatusDescription :
Content           : {"response":"100 entries inserted to the map with key: <your public ip>-* , starting from 1 "}
...
```
- Retrieve the data with the key format as `<your public ip>-1`, example:

```powershell
PS C:\Users\dtu.training\Documents\opentracing-hazelcast> http://localhost:8080/get?key=1.2.3.4-1

StatusCode        : 200
StatusDescription :
Content           : {"response":"1.2.3.4-1"}
...
```

- Execute a few more `get` transactions with different keys
- Once done, go back to the first terminal and terminate the Java program by using `CTRL+C`

```powershell
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  17:53 min
[INFO] Finished at: 
[INFO] ------------------------------------------------------------------------
Terminate batch job (Y/N)?
```
Enter Y

### Step 3: Explore Dynatrace PurePaths after auto instrumentation 
- Access your Dynatrace tenant in the browser
- Go to Transactions and Services > CommandController > Purepaths
- Choose the /pop1 transaction
- You will observe that the totoal transaction response time was 3 secs, but all of the Java method calls were less than 30 ms.
- Clearly something else is responsible

## Using OpenTracing instrumentation libaries
In this step we extend the Java SpringBoot application with an available OpenTracing instrumentation libary.

### Step 1: Decorate the java code with the HazelCast OpenTracing libaries
- Open `CommandController.java`
- Import the OpenTracing Libraries - Comment line 3 and 7
- Initialze the OpenTracing [GlobalTracer](https://github.com/opentracing/opentracing-java/blob/master/opentracing-util/src/main/java/io/opentracing/util/GlobalTracer.java) - Comment line 28 and 32
- Enhanc HazelcastInstance with [TracingHazelcastInstance](https://github.com/opentracing-contrib/java-hazelcast) - Comment line 35 and 42
- Save the file `CTRL+S`

### Step 2: Compile and run the application
- Run this command `mvn spring-boot:run`
- Check that the program compiles without any errors and it connects to the HazelCast nodes

### Step 3: Populate the HazelCast instance with new entries and retrieve the data
- Enter `curl http://localhost:8080/pop2`

```powershell
PS C:\Users\dtu.training\Documents\opentracing-hazelcast> curl http://localhost:8080/pop2

StatusCode        : 200
StatusDescription :
Content           : {"response":"100 entries inserted to the map with key: <your public ip>-* , starting from 200 "}
...
```
- Retrieve the data with the key format as `<your public ip>-200`, example:

```powershell
PS C:\Users\dtu.training\Documents\opentracing-hazelcast> http://localhost:8080/get?key=1.2.3.4-200

StatusCode        : 200
StatusDescription :
Content           : {"response":"1.2.3.4-200"}
...
```
- Execute a few more `get` transactions with different keys

### Step 4: Explore Dynatrace PurePaths after implementing OpenTracing libaries 
- Access your Dynatrace tenant in the browser
- Go to Transactions and Services > CommandController > Purepaths
- Choose the /pop2 transaction
- You will observe that we are getting more visibility into the application execution
