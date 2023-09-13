### Install and work with Postgresql on a Mac

1. Find out latest version of postgresql
2. brew install postgresql@<latest version number>
3. brew info postgresql<your version number>
4. Find location of postgresql and add to shell:                
`export PATH="/opt/homebrew/opt/postgresql@15/bin:$PATH"` 
4. brew services restart postgresql@15 will start Postgres
5. For compilers to find postgresql@15 you may need to set:  
  `export LDFLAGS="-L/opt/homebrew/opt/postgresql@15/lib"`  
  `export CPPFLAGS="-I/opt/homebrew/opt/postgresql@15/include"`
6. To get into a postgresql "shell" type: `psql postgres`

### Create a postgresql table

  CREATE TABLE name_address  
    (id SERIAL,  
    first VARCHAR(40),  
    last VARCHAR(40),  
    email VARCHAR(20),  
    state VARCHAR(2),  
    zip VARCHAR(5));
  
  ALTER TABLE public.name_address_id_seq OWNER TO "<Owner Name>";
  
  ALTER SEQUENCE public.name_address_id_seq OWNED BY public.name_address.id;
  
  ALTER TABLE ONLY public.name_address ALTER COLUMN id SET DEFAULT nextval('public.name_address_id_seq'::regclass);
  
  ALTER TABLE ONLY public.name_address
    ADD CONSTRAINT constraint_name UNIQUE (id);


#### Describe Table -- `\d <table name>`
#### Update Table with file 
  `COPY name_address FROM <full/path/to/file.csv> DELIMITER ',' CSV HEADER;`  
Create a .csv with all the columns.  Headers should match postgres column names. 

#### Queries
'select state, count(state) from name_address group by state;'  
will produce:    

| state | count | 
|-------|------ | 
| NY    |     2 |
| WA    |     2 |
| MA    |     2 |
| NJ    |     2 |
|  CA   |     2 |


#### Create table to join on
create table name_phones(np_id serial, id INT, phone_num varchar(10), phone_type varchar(10), primary key(np_id), CONSTRAINT fk_id FOREIGN KEY(id) REFERENCES name_address(id) on update cascade);

#### Upload and Query Data
select * from name_phone 

| np_id | id | phone_num  | phone_type|
|-------|--- |----------- |-----------|  
| 1     |  1 | 9171231234 | cell|
| 2     |  1 | 7181231234 | landline|
| 3     |  2 | 6461231234 | cell|
| 4     |  2 | 7181231234 | landline|
|  5    |  8 | 9172232234 | cell|

#### Inner Join

select first, last, state, zip, phone_num, phone_type from name_address inner join name_phones on name_phones.id = name_address.id;

 | first   |   last    | state |  zip  | phone_num  | phone_type |
|---------| --------- | ----- |------ | ---------- | ------------|
| Lois    | Greene    | NY    | 11104 | 9171231234 | cell|
| Lois    | Greene    | NY    | 11104 | 7181231234 | landline|
| Luis    | Hernandez | NY    | 11104 | 6461231234 | cell|
| Luis    | Hernandez | NY    | 11104 | 7181231234 | landline|
|  George | Costanza  | CA    | 90650 | 9172232234 | cell|

#### Left Join

select first, last, state, zip, phone_num, phone_type from name_address left join name_phones on name_phones.id = name_address.id;

|first  |   last    | state |  zip  | phone_num  | phone_type| 
|--------|-----------|-------|-------|------------|------------|
| Lois   | Greene    | NY    | 11104 | 9171231234 | cell|
| Lois   | Greene    | NY    | 11104 | 7181231234 | landline|
| Luis   | Hernandez | NY    | 11104 | 6461231234 | cell|
| Luis   | Hernandez | NY    | 11104 | 7181231234 | landline|
| George | Costanza  | CA    | 90650 | 9172232234 | cell|
| Fred   | Mertz     | WA    | 98052 |            | |
| Scott  | Kravits   | NJ    | 07713 |            | |
| Mary   | Jones     | NJ    | 08004 |            | |
| Lisa   | Cangemi   | MA    | 02148 |            | |
| Andrea | Kravits   | MA    | 02301 |            | |
| Harry  | Houdini   | WA    | 99301 |            | |
| Sally  | Harper    | CA    | 90011 |            | |
