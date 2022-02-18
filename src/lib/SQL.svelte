<script lang="ts">
  import initSqlJS from "sql.js";
  import sqlWasmUrl from "sql.js/dist/sql-wasm.wasm?url";
  import { onMount } from "svelte";
  import localforage from "localforage";

  localforage.config({
    name: "sqljs",
  });

  async function createDatabase() {
    const SQL = await initSqlJS({
      locateFile: () => sqlWasmUrl,
    });

    const keys = await localforage.keys();
    if (keys.includes("database")) {
      const currentData = await localforage.getItem("database");
      return new SQL.Database(currentData as Uint8Array);
    }

    const db = new SQL.Database();
    let sqlstr = `CREATE TABLE hello (a int, b char);
    INSERT INTO hello VALUES (0, 'hello');
    INSERT INTO hello VALUES (1, 'world');
    `;
    db.run(sqlstr);
    await localforage.setItem("database", db.export());
    return db;
  }

  onMount(async () => {
    const db = await createDatabase();

    const stmt = db.prepare("SELECT * FROM hello WHERE a=:aval AND b=:bval");
    const result = stmt.getAsObject({ ":aval": 1, ":bval": "world" });
    console.log(result);
    db.close();
  });
</script>
