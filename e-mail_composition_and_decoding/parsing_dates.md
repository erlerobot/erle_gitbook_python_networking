## Parsing Dates

The `email` package provides two functions that work together as a team to help you parse the Date field
of e-mail messages, whose format you can see in the preceding example: a date and time, followed by a
time zone expressed as hours and minutes (two digits each) relative to UTC. Countries in the eastern
hemisphere experience sunrise early, so their time zones are expressed as positive numbers, like the
following:
```
Date: Sun, 27 May 2007 11:34:43 +1000
```
Those of us in the western hemisphere have to wait longer for the sun to rise, so our time zones lag
behind; Eastern Daylight Time, for example, runs four hours behind UTC:
```
Date: Sun, 27 May 2007 08:36:37 -0400
```
To figure out what moment of time is really meant by a Date header, simply call two functions in a
row:

- Call `parsedate_tz()` to extract the time and time zone.
- Use `mktime_tz()` to add or subtract the time zone.
- The result with be a standard Unix timestamp.



For example, consider the two Date headers shown previously. If you just compared their bare
times, the first date looks later: 11:34 a.m. is, after all, after 8:36 a.m. But the second time is in fact the
much later one, because it is expressed in a time zone that is so much farther west. We can test this by
using the functions previously named. First, turn the top date into a timestamp:
```python
>>> from email.utils import parsedate_tz, mktime_tz
>>> timetuple1 = parsedate_tz('Sun, 27 May 2007 11:34:43 +1000')
>>> print timetuple1
(2007, 5, 27, 11, 34, 43, 0, 1, -1, 36000)
>>> timestamp1 = mktime_tz(timetuple1)
>>> print timestamp1
1180229683.0
Then turn the second date into a timestamp as well, and the dates can be compared directly:
>>> timetuple2 = parsedate_tz('Sun, 27 May 2007 08:36:37 -0400')
>>> timestamp2 = mktime_tz(timetuple2)
>>> print timestamp2
1180269397.0
>>> timestamp1 < timestamp2
True
```
If you have never seen a timestamp value before, they represent time very plainly: as the number of
seconds that have passed since the beginning of 1970. You will find functions in Python’s old time
module for doing calculations with timestamps, and you will also find that you can turn them into
normal Python datetime objects quite easily:
```python
>>> from datetime import datetime
>>> datetime.fromtimestamp(timestamp2)
datetime.datetime(2007, 5, 27, 8, 36, 37)
```
In the real world, many poorly written e-mail clients generate their Date headers incorrectly. While
the routines previously shown do try to be flexible when confronted with a malformed Date, they
sometimes can simply make no sense of it and `parsedate_tz()` has to give up and return None.
So when checking a real-world e-mail message for a date, remember to do it in three steps: first
check whether a Date header is present at all; then be prepared for None to be returned when you parse it;
and finally apply the time zone conversion to get a real timestamp that you can work with.
