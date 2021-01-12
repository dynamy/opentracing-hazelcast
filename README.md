# opentracing-hazelcast

## Hands on 1
prep environment
0. Deploy OneAgent
1. Clone repo via VS
2. Edit hazelcast-client.yaml in src > main > resources
3. Input public IP address `1.1.1.1`
4. Save the file CTRL+S

## Hands on 2
Compile and run app

1. Open terminal  Terminal > New Terminal
2. Run app `mvn spring-boot:run`
3. Open a new terminal tab
4. enter `curl http://localhost:8080/get?key=testkey`

this will be the response `{"response":null}`

5. enter `curl http://localhost:8080/populate`
{"response":"1000 entry inserted to the map with key: publicip-*  "}
6. retrieve repose when provided a key, enter `curl http://localhost:8080/get?key=1.1.1.1-43`
`{"response":1.1.1.1-43}`

Once done, terminate program
1. Clear the map `curl http://localhost:8080/clear`
{"response":"Map cleared"}
2. Go to VS
2. Switch to 1st terminal
3. CTRL+C, 
```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  17:53 min
[INFO] Finished at: 2020-12-29T16:57:07+08:00
[INFO] ------------------------------------------------------------------------
Terminate batch job (Y/N)?
```
Enter Y


## Hands on 3
Observability in Dynatrace without OpenTracing
11. Inspect dynatrace
Transactions and Services > CommandConytoller > Purepaths
12. Choose the /populate transaction, you will see all the SpringBoot calls, but no hazelcast calls
This is where we usually would say use the OneAgent Java SDK
But as shwon in the presentation eariler, OpenTracing framework is meant to solve such problems

## Hands on 4
Enable OpenTracing in Dynatrace
1. Open Settings > Server-side service monitoring > Deep Monitoring > OpenTelemetry and OpenTracing
2. Enable

## Hands on 5
Decorate code OpenTracing
1. Open `CommandController.java`
2. Comment line 3 and 7
line 28 and 32
line 35,36 and 43

3. CTRL+S Save
4. Run app `mvn spring-boot:run`
5. valid map has been cleared
curl http://localhost:8080/get?key=3.138.203.67-999
{"response":null}
else run clear command
6. enter `curl http://localhost:8080/populate`

## Hands on 6
Observability in Dynatrace with OpenTracing
11. Inspect dynatrace
Transactions and Services > CommandConytoller > Purepaths
12. Choose the /populate transaction, you will now see the hazelcast call

## Hands on 7
Create span attributes
1. Settings > Server-side service monitoring > Span attributes
2. Add item
enter key
3. execulte transactions
Inspect purepaths
