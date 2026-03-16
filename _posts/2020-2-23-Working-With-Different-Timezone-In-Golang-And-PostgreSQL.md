---
layout: post
title: Working with Different Timezones in Go and PostgreSQL
---

When your application serves users across multiple timezones — or integrates with data sources that record timestamps in varying timezone contexts — handling time correctly becomes critical. A subtle mismatch between your application's timezone and your database's timezone can cause silent data corruption: queries return incorrect result sets, scheduled jobs fire at wrong times, and bugs are nearly impossible to reproduce in local development.

Here are the strategies I follow to keep time data consistent and make retrieval predictable.

### Core Strategies

1. **Store all timestamps in a single timezone.** UTC is the standard choice. If you're using PostgreSQL, set the default database timezone to UTC explicitly rather than relying on system defaults.

2. **Convert to the database timezone before writing.** If your database is set to UTC, every timestamp must be converted to UTC before it's inserted. Never store local time directly.

3. **Convert query parameters to the database timezone before reading.** When filtering by time for a specific timezone, convert the query bounds to UTC first — otherwise you'll get the wrong result set.

4. **Convert result sets to the requested timezone after reading.** Transform timestamps back to the appropriate local timezone at the application layer, after the query returns.

### Example

Consider a `flights` table with `departure_time` and `arrival_time` fields, where departures and arrivals may originate from countries in different timezones.

The code below demonstrates how to convert, insert, query, and transform these timestamps correctly in Go:

<script src="https://gist.github.com/gyaan/93a00e88293d7af77f194eeb03dda7e6.js"></script>
