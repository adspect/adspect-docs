# Reporting

Our reports are a comprehensive and valuable source of analytical information on your affiliate campaigns performance
and traffic quality. You can use reports to evaluate traffic quality of different sources, publishers, ad spots, etc.
Reports come in two flavors: raw and aggregate.

Please note that statistical data is not real-time and is updated once every minute.

## Raw Reports

Raw reports are per-click, that is, they contain information on every click that was processed by
Adspect. They are available for download in the [CSV format](https://en.wikipedia.org/wiki/Comma-separated_values)
via the "Get CSV" button menu. There you have two options: get full report or only for those visitors that were
filtered by Adspect because of triggering one of more checks. Report will be limited by the selected date range.
Downloaded CSV files may then be imported into Microsoft Excel or similar spreadsheet software.

Please do not select too broad date ranges as it will lead to the formation of huge CSV files and additional strain
on our servers. We limit the total number of rows that will be included in report, and this limit is subject to
change at the sole discretion of our systems administrators.

## Raw Report Columns

Raw reports can have either one or two rows per each click. The first row corresponds to serving of our
fingerprint collector script for the client browser to execute. The second row, if present, corresponds to
fingerprint scanning and making the decision: allow or block. The second row may be missing if the visitor failed
to produce or submit a fingerprint.

Raw reports consist of the following columns:

* timestamp -- date and time of the event;
* stream_id -- ID of the stream that the event happened in;
* sub_id -- sub ID of the click;
* click_id -- unique ID of the click;
* sequence -- click processing stage: 0 for fingerprint collecting, 1 for fingerprint scan;
* certainty -- [VLA confidence](vla.md) in that the click is dangerous;
* precision -- [VLA precision](streams.md#vla) configured at the time of the click;
* valid -- 1 if the click has been allowed through, 0 otherwise (meaningful only if sequence = 1);
* tags -- list of mnemonic tags each identify a check that the click has failed.

## Aggregate Reports

Aggregate reports are produced by splitting raw reports into groups and summing metrics on per-group basis.
Grouping may be set set in the field to the left of the timezone selector and defaults to "Stream", meaning that
values will be computed on per-stream basis. Other grouping options include "Date" for per-date grouping and
"Sub ID" for per-subaccount grouping (refer to [this paragraph](streams.html#sub-id) for an idea of what may constitute
a subaccount.)

Grouping may be nested, e.g. "Date" followed by "Stream"--this will result in first splitting reports by date,
and then by stream on each date. You may combine grouping criteria in any way you need to inspect different funnels.

Press the "Get Report" button to produce an aggregate report in the form of a table that will be displayed to you
right below the report settings panel.

## Aggregate Report Columns

Each aggregate report consists of the grouping columns on the left site followed by a number of statistical columns.
Some of the statistical columns have a gray percentage value following a slash--that is percentage of the total clicks,
displayed for more comprehensivess.

The list of statistical columns, explained:

* Clicks -- the total number of clicks that accessed the `index.php` file.

* Fingerprints -- the number of visitors that successfully executed our JavaScript fingerprint collector code and
  submitted their fingerprints for analysis. This figure may be lower than the total number of clicks for various
  reasons, most often it being the inability of dumb click bots to run JavaScript.

* Money hits -- how many visitors have been shown the money page. This is the best metric for accounting legitimate
  traffic. Please note that this also includes all visitors when the stream works in the "All money" mode.

* White hits -- how many visitors have or would have been shown the white page. This metric is calculated as
  clicks minus money hits and includes those dumb bots that would have been show the white page if they were
  able to execute JavaScript (there's a fallback "meta refresh" mechanism to deal with them.)

* Tech. loss -- technical loss, which is the number of visitors that failed to produce a fingerprint. As mentioned
  above, these are often dumb bots with limited JavaScript support. Another common reason is network latency,
  especially evident in traffic with slow connection rates when visitors manage to close the tab or window before
  their fingerprint is submitted.

* Filtered -- the number of fingerprints that Adspect consciously filtered out as bad traffic. This may serve as
  a rough traffic quality metric with respect to the more advanced types of click fraud that get more spread today.

* Quality -- percentage of money hits in the whole click volume. This is the the best metric for evaluating
  traffic quality as a whole and may be used to compare different traffic sources, publishers, ad spots, etc.
  Especially useful with grouping by sub ID for compiling blacklists of bot-ridden zones, as described in
  a [dedicated parapgraph](use-cases.html#detecting-bot-zones).

There's also the "CSV" button in the bottom left corner of the report box. You can use it to download CSV versions
of aggregate reports for further analysis, printing, or sharing.
