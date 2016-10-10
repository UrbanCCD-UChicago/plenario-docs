## Bulk Data Export

`GET /v1/api/sensor-networks/<network_name>/download`

Because the raw data endpoint /query is designed to respond quickly,
it is limited to 1000 observations.
It also only lets you query over one feature of interest per call.
If you want a larger dump of data,
potentially over a long period of time and over many features,
you can use /download to request a bulk export.

(Insert ticket response example here)

/download returns a ticket that you can use to check on your request's progress
at `/v1/api/jobs/{ticket}`.
When the export is complete, `/v1/api/jobs/{ticket}` will give you a link where you can download the file.
