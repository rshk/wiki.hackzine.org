PostgreSQL: Change default schema
#################################

Refer to `official documentation <http://www.postgresql.org/docs/9.1/static/ddl-schemas.html>`_ too.

.. code-block:: sql

    -- Use this to show the current search_path
    -- Should return: "$user",public
    SHOW search_path;

    -- Create another schema
    CREATE SCHEMA my_schema;
    GRANT ALL ON SCHEMA my_schema TO my_user;

    -- To change search_path on a connection-level
    SET search_path TO my_schema;

    -- To change search_path on a database-level
    ALTER database "my_database" SET search_path TO my_schema;
