# Database
  - Oracle
  - MySQL
  - PostgreSQL

---


# Oracle

# MySQL

# PostgreSQL
## PL/SQL
  - example
  ```
  do $$
  declare
      v_db_name             varchar(200) := 'svcm';
      v_table_name          varchar(200) := 'sm_ctmmny';

      v_dmn_conect_url_cnt  integer := 0;
      v_dmn_conect_url      varchar(200) := 'https://svcm-fo.spharos-dev.mycloudmembership.com/';
  begin
      raise notice '========== plsql 실행합니다.[%] ==========', v_dmn_conect_url;

      select count(*)
        into v_dmn_conect_url_cnt
        from information_schema.columns
       where table_catalog = v_db_name
         and table_name    = v_table_name
      ;

      if v_dmn_conect_url_cnt < 0 then
          raise notice '= add column dmn_conect_url';
          alter table svcm.sm_ctmmny add column dmn_conect_url varchar(200);
      end if;


      raise notice '= set column value';
      update
          svcm.sm_ctmmny
      set
          dmn_conect_url = v_dmn_conect_url;


      raise notice '= set column dmn_conect_url set not null';
      alter table svcm.sm_ctmmny alter column dmn_conect_url set not null;
  end $$;
  ```
  
  - 참고사이트: [https://postgresql.kr/docs/9.3/plpgsql.html](https://postgresql.kr/docs/9.3/plpgsql.html)
