# Errors


The iPay Gateway uses the following error codes:


Error Code | Meaning
---------- | -------
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Uniavailable -- We're temporarily offline for maintenance. Please try again later.
GW-001 | merchant_key missing or empty
GW-002 | invoice_id missing or empty
GW-003 | total missing or empty
GW-004 | invoice secret missing or empty
GW-005 | non-unique invoice or invoice could not be retrieved
GW-006 | no payment option provided
GW-007 | invalid payment option
GW-008 | specified merchant_key not valid for current site

