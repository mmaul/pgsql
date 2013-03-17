NAME: pgsql

VERSION: .01  

AUTHOR: Mike Maul

AUTHOR_EMAIL: mike.maul@gmail.com

PKG_URL: https://github.com/mmaul/pgsql

DESCRIPTION: PostgreSQL binding for the felix language

CATEGORY: database

LIBDIR: PGSQL

-----
Lowlevel bindings for PostgreSQL

## Quickstart Installation ##
* 'install' must be able to write to Felix INSTALL_ROOT

    scoop get pgsql
    scoop build pgsql
    scoop install pgsql

## Documentation ##
See PGSQL/psql.flx and examples/testdb.flx
However here is a quick example

    include "PGSQL/pgsql";
    include "PGSQL/pgsql_pool";
    open Pgsql;
    open PgsqlPool;
    var conn_str = "host=127.0.0.1 port=5432 dbname=template1 user=dbuser password=password";
    var db_pool = PgsqlPool::create_pgsql_pool(conn_str,size(1));
    // Get DB handle 
    match db_pool.get_next() with
    |Some handle => 
      val qry = """SELECT id,name,email FROM mailing_list""";
      val rslt = pq_exec(handle,qry);
      match pq_result_status(rslt) with
      |$(PGRES_TUPLES_OK) => 
        for var r in 0 upto (pq_ntuples(rslt)-1) do
          println$ pq_getvalue(rslt,r,0)+" , "+pq_getvalue(rslt,r,1)+ 
            ","+pq_getvalue(rslt,r,2);
        done
      |_ => println$ "QUERY FAILED";
      endmatch;
      destroy_member handle;
    |_ => println$ "CONNECT FAILED";
    endmatch;
    
  

