# Guide: GTFS → Linked Connections

Transforms a GTFS file towards a directed acyclic graph of "connections".

A connection is the combination of a departure and its successive arrival of the same trip. 
Our goal is to retrieve a list of connections that is sorted by departure time, better known as a Directed Acyclic Graph. This way, routeplanning algorithms can be performed.

## Install it

```
npm install -g gtfs2lc
```

## Use it

First, __unzip__ your GTFS file to a place on your disk using e.g., `unzip gtfs.zip /tmp`

Now, we need to make sure that a couple of files are ordered in a specific fashion, not required by the GTFS spec. You can do these orderings through bash as follows:
 * __stop_times.txt__ must be ordered by `trip_id` and `stop_sequence`. You can do this using this command: `{ head -n 1 stop_times.txt ; tail -n +2 stop_times.txt | sort; } > stop_times2.txt ; mv stop_times2.txt stop_times.txt`. Mind that the number of the columns are also not standardized by GTFS and you might need to tweak the sort command.
 * __trips.txt__ must be ordered by `trip_id`.  You can do this using this command: `{ head -n 1 trips.txt ; tail -n +2 trips.txt | sort; } > trips2.txt ; mv trips2.txt trips.txt`.
 * __calendar.txt__ must be ordered by `service_id`.  You can do this using this command: `{ head -n 1 calendar.txt ; tail -n +2 calendar.txt | sort; } > calendar2.txt ; mv calendar2.txt calendar.txt`.
 * __calendar_dates.txt__ must be ordered by `service_id`.  You can do this using this command: `{ head -n 1 calendar_dates.txt ; tail -n +2 calendar_dates.txt | sort; } > calendar_dates2.txt ; mv calendar_dates2.txt calendar_dates.txt`.

If you've ensured this, you can install this library using: `npm install -g gtfs2lc` and use it as follows:

```bash
gtfs2lc -p /path/to/extracted/gtfs -f csv
```

For more options, check `gtfs2lc --help`

## How it works (for contributors)

We convert `stop_times.txt` to a stream of connection rules. These rules need a certain explanation about on which days they are running, which can be retrieved using the `trip_id` in the connection rules stream.

At the same time, we process `calendar_dates.txt` and `calendar.txt` towards a binary format. It will contain a 1 for the number of days from a start date for which the service id is true.

In the final step, the connection rules are expanded towards connections by joining the days, service ids and rules.

## Not yet implemented:

At this moment we've only implemented a conversion from the Stop Times to connections. However, in future work we will also implement a system for describing trips and routes, a system for transit stops and a system for transfers.

Furthermore, also `frequencies.txt` is not supported at this time. We hope to support this in the future though.

## Authors

Pieter Colpaert - pieter.colpaert@ugent.be
