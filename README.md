# Hoen Scanner
A small Dropwizard microservice that loads rental car and hotel search
results from `rental_cars.json` and `hotels.json`, and exposes a
`POST /search` endpoint that filters results by city.

## Project layout
```
hoen-scanner/
├── pom.xml
├── config.yml
├── src/main/java/com/skyscanner/
│   ├── HoenScannerApplication.java   # entry point, wires everything up
│   ├── HoenScannerConfiguration.java # Dropwizard config class
│   ├── Search.java                   # deserialised request body ({"city": "..."})
│   ├── SearchResult.java             # serialised response item (city/kind/title)
│   └── SearchResource.java           # /search endpoint
└── src/main/resources/
    ├── rental_cars.json
    └── hotels.json
```
## Opening in IntelliJ
1. `File > Open` and select the `hoen-scanner` folder (or `pom.xml`).
2. When prompted, click **Load Maven Project**.
3. Make sure an OpenJDK 19 SDK is configured (`File > Project Structure > SDKs`).

## Running

Because Dropwizard apps take a command (`server`) and a config file path as
program arguments, set those up in your Run Configuration:

1. `Run > Edit Configurations...`
2. Create an **Application** configuration:
   - Main class: `com.skyscanner.HoenScannerApplication`
   - Program arguments: `server config.yml`
   - Working directory: the project root (so `config.yml` resolves)
3. Click the green run arrow.

You should see Dropwizard's startup logs followed by:

```
Welcome to Hoen Scanner!
```

The service listens on `localhost:8080` (admin/metrics on `localhost:8081`).

## Testing with Postman

1. Create a new **POST** request to `localhost:8080/search`.
2. Body → raw → JSON:
   ```json
   {"city": "petalborough"}
   ```
3. Send. You should get back a JSON array of matching car and hotel results.

Try `rustburg` and `shaleport` too (all lowercase, matching the sample data),
as well as an unknown city to confirm an empty list `[]` comes back, and a
malformed body (e.g. missing `city`) to confirm validation kicks in (400
response, since `Search.city` is marked `@NotNull`/`@Valid` on the resource
method).

## Sample data

`rental_cars.json` and `hotels.json` contain a handful of made-up listings
for `petalborough`, `rustburg`, and `shaleport` so the endpoint has data to
filter against out of the box. Swap in the real files from the starter repo
if you have them — the format (`city`, `kind`, `title`) matches what
`SearchResult` expects.
