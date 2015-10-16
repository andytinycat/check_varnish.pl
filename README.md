# check_varnish.pl - Nagios plugin for monitoring Varnish Cache v4.0

This Nagios plugin reads the output of 'varnishstat -1', which presents a one-shot
look at all the internal counters that Varnish has.

This version of the nagios plugin works only with Varnish **v4.0**.

It can monitor any of these counters, and you can add warning & critical values for
each counter individually.

It will return Nagios perfdata for each counter requested.

Additionally, there is one 'special' counter that doesn't exist in the varnishstat -1 output -
this is 'AUX.cache_hit_percent', which is calculated like this:

     AUX.cache_hit_percent = ( MAIN.cache_hit / ( MAIN.cache_hit + MAIN.cache_miss ) ) * 100

# Usage
## Examples
  ```
    ./check_varnish.pl --spec=AUX.cache_hit_percent --spec=MAIN.n_lru_nuked --spec=MAIN.sess_dropped --spec=MAIN.client_req
  ```

## Options
```
	check_varnish.pl --spec=[spec definition] --spec=[spec definition]
	check_varnish.pl --list
	check_varnish.pl --help
```

```
    --spec=[spec definition], -s [spec definition]
        Specifies which Varnish counter to monitor. A spec definition looks like this:

            --spec=counter_name,warnvalue,critvalue,[u|d]

        The "u" or "d" option at the end determines whether you want to consider the warn and crit values as met
        when the counter is above ('u' - up) or below ('d' - down) these values.
        e.g. for failed backend requests, you'd want to use the 'u' option (since more failed backend
        requests is bad thing), but for the cache_hit_percent you'd want to use 'd', since you'll only
        warn when the cache isn't serving as many requests from memory as you'd expect.

        If no 'u' or 'd' option is present, 'u' is assumed.

        If you don't want to set warn/crit values (i.e. just want perfdata output), then use this form:

            --spec=counter_name

        To monitor multiple counters, use multiple --spec options. e.g.

            --spec=counter_one --spec=counter_two,50,80 --spec=counter_three

        The plugin will return a WARNING state if any one counter is over its warn value, and no counters
        are over the critical value. Similarly, if any one counter is over its crit value, the plugin
        will return a CRITICAL state.

    --list
        This option prints a list of all the available Varnish counters available, and a brief
        description of what each one is for (taken from the varnishstat output).

    --help
        Displays this message.

check_varnish.pl comes with absolutely NO WARRANTY either implied or explicit
This program is licensed under the terms of the
GNU General Public License (check source code for details)
```
