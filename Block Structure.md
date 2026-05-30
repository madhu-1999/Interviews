#sql  #postgres
# Syntax
```sql
$$
DECLARE
-- declare variables
BEGIN
-- body
END;
$$;
```
# Declaring Variables
We can declare variables as:
```sql
...
DECLARE
	counter integer = 1;
	first_name varchar(50); -- intialization is optional
	film_title film.title%type; -- film_title has same type as film.title
```
# Assign value to variables
```sql
select col1, col2, ...
into var1, var2, ...
from table_expr;
```

Ex:
```sql
do
$$
declare
   v_first_name actor.first_name%type;
   v_last_name actor.last_name%type;
begin
   -- select the first_name and last_name of the actor id 1
   select first_name, last_name
   into v_first_name, v_last_name
   from actor
   where actor_id = 1;

   -- show the full name
   raise notice '% %', v_first_name, v_last_name;
end;
$$;
```
# Row type variables
They can store an __entire row__ returned by a `SELECT INTO` or `FOR` statement.
```sql
-- Declaring row variable
row_variable table_name%ROWTYPE;
row_variable view_name%ROWTYPE;

-- Accessing a column value
row_variable.col_name
```

Ex:
```sql
do
$$
declare
	selected_actor actor%ROWTYPE;
begin
	-- select actor with id 10
	select *
	into selected_actor
	from actor
	where actor_id = 10;

	--show the number of actor
	raise notice 'The actor name is % %', 
	selected_actor.first_name,
	selected_actor.last_name;
end;
$$;
```
# Record Type variables
Unlike row type variables, record type variables __do not have a predefined schema__.
Like row type variables they hold __only one row__ at a time.

```sql
-- declaring record type variable
variable_name record;

--accessing column value
-- If you attempt to access a column that doesn't exist an error is raised.
variable_name.col_name
```

```sql
do
$$
declare
	rec record;
begin
	-- select the film
	select film_id, title, length
	into rec
	from film
	where film_id = 200;
	
	raise notice '% % %', rec.film_id, rec.title, rec.length;
end;
$$;
```
# Declaring constants
```sql
...
declare
	constant_name constant type = inital_value;
...

-- Examples
started_at constant time = clock_timestamp();
vat constant numeric = 0.1;
```
# Reporting Errors and Messages
```sql
raise level format
--Allowed level values:
-- debug
-- log
-- notice
-- info
-- warning
-- exception (default)
```
Ex:
```sql
do $$
begin
  raise info 'information message %', now() ;
  raise log 'log message %', now();
  raise debug 'debug message %', now();
  raise warning 'warning message %', now();
  raise notice 'notice message %', now();
  raise invalid_regular_expression; -- raises exception
end $$;
```
# IF Statement
```sql
if condition then
	statements;
elsif condition then
	statements;
else
	statements;
end if;
```

```sql
do $$
declare
   v_film film%rowtype;
   len_description varchar(100);
begin
  select * from film
  into v_film
  where film_id = 100;

  if not found then
     raise notice 'Film not found';
  else
      if v_film.length >0 and v_film.length <= 50 then
		 len_description := 'Short';
	  elsif v_film.length > 50 and v_film.length < 120 then
		 len_description := 'Medium';
	  elsif v_film.length > 120 then
		 len_description := 'Long';
	  else
		 len_description := 'N/A';
	  end if;

	  raise notice 'The % film is %.',
	     v_film.title,
	     len_description;
  end if;
end;
$$;
```
# CASE Statement
```sql
do $$
declare
	rate film.rental_rate%type;
	price_segment varchar(50);
begin
    -- get the rental rate
    select rental_rate into rate
    from film
    where film_id = 100;

	-- assign the price segment
	if found then
		case rate
		   when 0.99 then
              price_segment =  'Mass';
		   when 2.99 then
              price_segment = 'Mainstream';
		   when 4.99 then
              price_segment = 'High End';
		   else
	    	  price_segment = 'Unspecified';
		   end case;

		raise notice '%', price_segment;
	else
		raise notice 'film not found';
    end if;
end; 
$$;
```
# Unconditional loop
```sql
<<outer>>
loop
   statements;
   <<inner>>
   loop
     /* ... */
     exit <<inner>>
   end loop;
   exit <<outer>>
end loop;
```

```sql
do
$$
declare
	row_var int = 0;
	col_Var int = 0;
begin
	<<outer_loop>>
	loop
		row_var = row_var + 1;
		<<inner_loop>
		loop
			col_var = col_var + 1;
			raise notice '(%, %)', row_var, col_var;
		exit inner_loop when col_var = 3;
		end loop;
		-- reset col_var
		col_var = 0;
		exit outer_loop when row_var = 3;
	end loop;
end;
$$;
```
# While loop
```sql
[ <<label>> ] 
while condition 
	loop 
		statements; 
	end loop;
```

```sql
do
$$
declare 
	counter integer = 0;
begin
	while counter < 5 loop
		counter = counter + 1;
	end loop;
end;
$$;
```
# For loop
```sql
do
$$
--loop from 1 to 5 step=2
begin
   for counter in 1..5  by 2 loop
	raise notice 'counter: %', counter;
   end loop;2
end;
$$;

-- o/p
-- counter: 1
-- counter: 3

-- loop from 5 to 1 step=1
do $$
begin
   for counter in reverse 5..1 loop
      raise notice 'counter: %', counter;
   end loop;
end; 
$$;
```
## Loop over result set
```sql
do
$$
declare
	f record;
begin
	for f in select title, length
		from film
		order by length desc, title
		limit 10
	loop
		raise notice '%, %', f.title, f.length;
	end loop;
end;
$$;
```
# Continue Statement
>[!note] `exit` is equivalent to `break`

```sql
do
$$
declare
   counter int = 0;
begin

  loop
     counter = counter + 1;

	 -- exit the loop if counter > 10
	 exit when counter > 10;

	 -- skip the current iteration if counter is an even number
	 continue when mod(counter,2) = 0;

	 -- print out the counter
	 raise notice '%', counter;
  end loop;
end;

$$;
```
# Handling exceptions
```sql
do
$$
declare
	rec record;
	v_length int = 30;
begin
	-- select a film
	select film_id, title
	into strict rec
	from film
	where length = v_length;

        -- catch exception
	exception
	   when sqlstate 'P0002' then
	      raise exception 'film with length % not found', v_length;
	   when sqlstate 'P0003' then
	      raise exception 'The with length % is not unique', v_length;
end;
$$;
```

