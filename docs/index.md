# [*Deutsche Bahn*](https://en.wikipedia.org/wiki/Deutsche_Bahn) Public Transport API

**The public endpoint is [`1.db.transport.rest`](`https://1.db.transport.rest`).** This API returns data in the [*Friendly Public Transport Format* `1.0.1`](https://github.com/public-transport/friendly-public-transport-format/blob/1.0.1/spec/readme.md).

*Note:* In order to improve this API, I would to know which software projects use it. Please send an **`X-Identifier` header (e.g. `my-awesome-tool`) to let me know who you are**. I you don't provide it, a hash of the client IP will be logged.

## all routes

- [`GET /stations?query=…`](#get-stationsquery)
- [`GET /stations`](#get-stations)
- [`GET /stations/:id/departures`](#get-stationsiddepartures)
- [`GET /journeys`](#get-journeys)
- [`GET /locations`](#get-locations)

## `GET /stations?query=…`

Passes all parameters into [`db-stations-autocomplete`](https://github.com/derhuerst/db-stations-autocomplete).

- `query`: **Required.**
- `completion`: `true`/`false` – Default is `true`
- `fuzzy`: `true`/`false` – Default is `false`

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/stations?query=jungfernheide'
# note the typo
curl 'https://1.db.transport.rest/stations?query=jungfernhiede&fuzzy=true'
```


## `GET /stations`

Passes all parameters into [`db-stations`](https://github.com/derhuerst/db-stations).

- `id`: Filter by ID.
- `name`: Filter by name.
- `coordinates.latitude`: Filter by latitude.
- `coordinates.longitude`: Filter by longitude.
- `weight`: Filter by weight.

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/stations?name=hannover&coordinates.latitude=52.3765387'
```


## `GET /stations/all`

Dumps `full.json` from [`vbb-stations`](https://github.com/derhuerst/vbb-stations).

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/stations/all'
```


## `GET /stations/:id/departures`

Returns departures at a station.

*Note:* As stated in the [*Friendly Public Transport Format* `1.0.1`](https://github.com/public-transport/friendly-public-transport-format/tree/1.0.1), the returned `departure` and `arrival` times include the current delay.

Passes all parameters into [`departures(…)` from `db-hafas`](https://github.com/derhuerst/db-hafas/blob/master/docs/departures.md).

- `when`: A [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) or anything parsable by [`parse-messy-time`](https://github.com/substack/parse-messy-time#example). Default: now.
- `duration`: Show departures for the next `n` minutes. Default: `10`.

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/stations/008011160/departures?when=tomorrow%206pm'
```


## `GET /journeys`

Output from [`require('db-hafas').journeys(…)`](https://github.com/derhuerst/db-hafas#getting-started). Start location and end location must be either in [station format](#station-format) or in [POI/address format](#poiaddress-format) (you can mix them).

*Note:* As stated in the [*Friendly Public Transport Format* `1.0.1`](https://github.com/public-transport/friendly-public-transport-format/tree/1.0.1), the returned `departure` and `arrival` times include the current delay.

## station format

- `from`: **Required.** Station ID (e.g. `008011162`).
- `to`: **Required.** Station ID (e.g. `008011162`).

## POI format

- `from.latitude`/`to.latitude`: **Required.** Latitude (e.g. `52.543333`).
- `from.longitude`/`to.longitude`: **Required.** Longitude (e.g. `13.351686`).
- `from.name`/`to.name`: Name of the locality (e.g. `Atze Musiktheater`).
- `from.id`/`to.id`: **Required.** POI ID (e.g. `991598902`).

## address format

- `from.latitude`/`to.latitude`: **Required.** Latitude (e.g. `52.543333`).
- `from.longitude`/`to.longitude`: **Required.** Longitude (e.g. `13.351686`).
- `from.address`/`to.address`: **Required.** Address (e.g. `Voltastr. 17`).

## other parameters

- `when`: A [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time) or anything parsable by [`parse-messy-time`](https://github.com/substack/parse-messy-time#example). Default: now.
- `results`: Maximum number of results. Default: `5`.
- `via`: Station ID. Default: `null`.
- `passedStations`: Return stations on the way? Default: `false`.
- `transfers`: Maximum number of transfers. Default: `5`.
- `transferTime`: Minimum time in minutes for a single transfer. Default: `0`.
- `accessibility`: Possible values: `partial`, `complete`. Default: `none`.
- `bike`: Return only bike-friendly journeys. Default: `false`.
- `tickets`: Return information about available tickets. Default: `false`.

taxi
tram
bus
ferry
subway
suburban
regional
regionalExp
national
nationalExp
- `taxi`: Include taxis? Default: `false`.
- `tram`: Include [trams](https://en.wikipedia.org/wiki/Tram)? Default: `true`.
- `ferry`: Include [ferries](https://en.wikipedia.org/wiki/Ferry)? Default: `true`.
- `bus`: Include [buses](https://en.wikipedia.org/wiki/Bus)? Default: `true`.
- `subway`: Include [U-Bahn trains](https://en.wikipedia.org/wiki/Rapid_transit)? Default: `true`.
- `suburban`: Include [S-Bahn trains](https://en.wikipedia.org/wiki/S-train)? Default: `true`.
- `regional`: Include RB/ODEG trains? Default: `true`.
- `regionalExp`: Include RE/ODEG trains? Default: `true`.
- `national`: Include [IC/EC trains](https://en.wikipedia.org/wiki/Intercity_(Deutsche_Bahn)? Default: `true`.
- `nationalExp`: Include [ICE trains](https://en.wikipedia.org/wiki/Intercity-Express)? Default: `true`.

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/journeys?from=008011162&to=008000281'
curl 'https://1.db.transport.rest/journeys?from=008004158&to.name=Atze%20Musiktheater&to.latitude=52.543333&to.longitude=13.351686'
curl 'https://1.db.transport.rest/journeys?from=…&to=…&results=3&bus=false&tickets=true'
```


## `GET /locations`

Output from [`require('db-hafas').locations(…)`](https://github.com/derhuerst/db-hafas/blob/master/docs/locations.md)

- `query`: **Required.** (e.g. `Alexanderplatz`)
- `results`: How many stations shall be shown? Default: `10`.
- `stations`: Show stations? Default: `true`.
- `poi`: Show points of interest? Default: `true`.
- `addresses`: Show addresses? Default: `true`.

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/locations?query=Alexanderplatz'
curl 'https://1.db.transport.rest/locations?query=Pestalozzistra%C3%9Fe%2082%2C%20Berlin&poi=false&stations=false'
```


## `GET /radar`

- `north`: **Required.** Northern latitude.
- `west`: **Required.** Western longtidue.
- `south`: **Required.** Southern latitude.
- `east`: **Required.** Eastern longtidue.
- `results`: How many vehicles shall be shown? Default: `256`.
- `duration`: Compute frames for how many seconds? Default: `30`.
- `frames`: Number of frames to compute. Default: `3`.

`Content-Type`: `application/json`

### examples

```shell
curl 'https://1.db.transport.rest/radar?north=52.52411&west=13.41002&south=52.51942&east=13.41709'
```
