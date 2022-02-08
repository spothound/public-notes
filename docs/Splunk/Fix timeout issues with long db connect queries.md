Splunk has an application called "Splunk DB Connect", you can configure it to make DB queries to any DB. 

![[Pasted image 20220208171116.png]]

By default, this app has a query timeout of 30 seconds and, sometimes, your queries will grow in time required to retrieve an answer and will fail with an error very similar to the following one:

`java.sql.SQLTransientConnectionException: unnamed_pool_767188343_jdbc__postgresql__//my-database-read-replica.aaamj1isejlf.eu-west-2.rds.amazonaws.com__5432/myquery - Connection is not available, request timed out after 30000ms.`

This can be fixed by increasing the ****query_timeout**** parameter in **`db_inputs.conf`** configuration file. Depending on your Splunk environment architecture, you will need to do this change in one instance or another, but you will typically want to do the change in the **search instance**. 

This parameter **is not configurable through the web interface** you need to login into de search instance and modify the file manually. Then, you will need to restart the **Splunkd** service to make the change effective and consider that you will lose any change made to the default files on updates, so refer to [splunk docs](https://docs.splunk.com/Documentation/Splunk/8.2.4/Admin/AboutConfigurationFiles) to know how to make this change permanent.

As for an example on how to fix this in a splunk environment configured with systemd:

```
# locate the configuration file
find / -name "db_inputs.conf" 2> /dev/null

# edit the file
vi /opt/splunk/etc/apps/splunk_app_db_connect/default/db_inputs.conf
```

Increase the query_timeout field. By default, it should be in 30. You can push it to 90, for example.

```
query_timeout = 90
```