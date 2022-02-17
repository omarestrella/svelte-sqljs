<script lang="ts">
  import initSqlJS from "sql.js";
  import sqlWasmUrl from "sql.js/dist/sql-wasm.wasm?url";
  import { onMount } from "svelte";

  onMount(async () => {
    const SQL = await initSqlJS({
      locateFile: () => sqlWasmUrl,
    });

    const db = new SQL.Database();
    let sqlstr =
      "CREATE TABLE hello (a int, b char); \
  INSERT INTO hello VALUES (0, 'hello'); \
  INSERT INTO hello VALUES (1, 'world');";
    db.run(sqlstr);
    const stmt = db.prepare("SELECT * FROM hello WHERE a=:aval AND b=:bval");
    // Bind values to the parameters and fetch the results of the query
    const result = stmt.getAsObject({ ":aval": 1, ":bval": "world" });
    console.log(result);
  });
</script>
