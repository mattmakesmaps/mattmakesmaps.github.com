---
date: 2010-06-28
title: PL/pgSQL function to Iterate pgRouting
---

I've been working on a side project using [pgRouting](http://www.mkgeomatics.com/wordpress/?s=pgRouting&searchsubmit=Find) to determine the least-cost path across a street network from a given building to the nearest bus stop within [Bellingham, WA](http://en.wikipedia.org/wiki/Bellingham,_Washington). It's one thing to execute pgRouting's built-in functions for a single vertex (building) to another vertex (bus stop)... but another to have the function iterate through all buildings and their closest bus stop. <!-- more -->

So that began my first experience with using PL/pgSQL. The benefit for using the procedural language for PostgreSQL lies in its ability to loop through collections of records easily. I've posted my function below. It's not pretty, but it's filled with enough notices to let me know where an error occurs, which helped me understand how things were acting each step of the way. Here is the basic idea:



	
  * Loop through a table in which each row has a source and destination vertex

	
  * Execute the pgRouting function using these two vertices, determining the length of the least-cost path.

	
  * Populate a field, 'dist_calc' with the distance.


[sourcecode]
CREATE OR REPLACE FUNCTION bulk_route_generate() RETURNS VOID AS $$
DECLARE
 bld_row bld_wtastops_staging5%ROWTYPE;
 dist_calc RECORD;
BEGIN
 RAISE NOTICE 'Beginning Function';
 FOR bld_row IN SELECT * FROM bld_wtastops_staging5 WHERE bld_wtastops_staging5.bld_vert_node IS NOT NULL
 AND bld_wtastops_staging5.wtastops_vert_node IS NOT NULL
 -- BEGIN ADDING BUM NODES TO SKIP OVER
 AND bld_wtastops_staging5.bld_vert_node &lt;&gt; 2915
 AND bld_wtastops_staging5.wtastops_vert_node &lt;&gt; 293
 -- ADD START GID
 -- USED ONLY IF BUM NODES EXIST
 --AND bld_wtastops_staging5.bld_gid &gt;= 29200
 ORDER BY bld_wtastops_staging5.bld_gid LOOP
 RAISE NOTICE 'Value of wtastops_vert_node is %. The value of bld_vert_node is %',bld_row.wtastops_vert_node, bld_row.bld_vert_node;
 RAISE NOTICE 'Value of wtastops_gid is %. The value of bld_gid is %',bld_row.wtastops_gid, bld_row.bld_gid;
 -- BEGIN STANDARD pgRouting A*Star FUNCTION
 SELECT SUM(cost) INTO dist_calc FROM shortest_path_astar('
 SELECT gid as id,
 source::integer,
 target::integer,
 length::double precision as cost,
 x1, y1, x2, y2
 FROM streets_9102748',
 bld_row.bld_vert_node, bld_row.wtastops_vert_node, false, false);
 RAISE NOTICE 'Value of dist_calc is %.',dist_calc;
 EXECUTE 'UPDATE bld_wtastops_staging5
 SET route_dist = ' ||dist_calc|| '
 WHERE ' ||bld_row.bld_gid|| ' = bld_wtastops_staging5.bld_gid';
 END LOOP;
 -- BAIL OUT ON ERRORS
 EXCEPTION
 WHEN CONTAINING_SQL_NOT_PERMITTED THEN
 RAISE NOTICE ' EXECPTION Value of wtastops_vert_node is %. The value of bld_vert_node is %',bld_row.wtastops_vert_node, bld_row.bld_vert_node;
END;
$$ LANGUAGE 'plpgsql';
-- EXECUTE FUNCTION
SELECT bulk_route_generate();
[/sourcecode]

I'm excited at the possibilities that using PL/pgSQL offers in terms of manipulating data. I'm sure that the above function can be cleaned up quite a bit, too. If I ever have the need to re-visit this or similar problems, I'll be sure to do some serious head-scratching to think about a better approach!

Here is an image of the resulting data generated using [mapnik](http://mapnik.org/). Areas from dark green to light-green are within a 1/4 mile distance, while areas from yellow-to-red represent distances increasingly greater then a 1/4 mile. The large checkered areas are where the dataset failed to route. More on that at another time.

[caption id="attachment_360" align="alignnone" width="430" caption="the result as seen in mapnik"][![bld_sym_diverging_web](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/06/bld_sym_diverging_web.png)](http://www.mkgeomatics.com/wordpress/wp-content/uploads/2010/06/bld_sym_diverging_web.png)[/caption]
