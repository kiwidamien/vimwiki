# Issues with brew and postgres

## Startup -- cannot find a formula for postgres

(this is an older issue, need to document fix after reproducing it)

## Port in use

I have seen postgres hogging port `5432` as it's default port, and this prevents some docker images that are trying to use their instance of airflow from working.

The advice is to shutdown postgres, but on my current system `brew services stop postgresql` gives an error message.
The way to solve is 
```bash
$ brew services list
# lists postgresql@14 as the name of the service
$ brew services stop postgresql@14
# tada!!
```
