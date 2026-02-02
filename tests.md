## Tests v0.1

**1. Engine Lifecycle & Crash Recovery**<br>
`TestEngineCrashRecoveryFromWAL`<br>
Validates that the engine can fully reconstruct its in-memory state by replaying the WAL after a simulated crash, ensuring durability of acknowledged writes.

`TestEngineCrashRecoveryWithDelete`<br>
Ensures that delete operations (tombstones) are correctly persisted in the WAL and restored during crash recovery.

`TestRecoveryIsIdempotent`<br>
Verifies that running recovery multiple times produces the same engine state, proving recovery is deterministic and safe.

`TestPhase0EngineOpenCreatesDirectories`<br>
Confirms that engine initialization creates all required on-disk directories (WAL, SSTables, metadata) if they do not already exist.


**2. Engine Write Semantics**<br>
`TestEnginePutAndDelete`<br>
Tests basic Put and Delete operations at the engine layer, ensuring correct write routing to WAL and memtable.<br>

`TestEngineSequenceIncrements`<br>
Ensures the global sequence number strictly increases for every write, maintaining total order across updates.<br>

**3. WAL (Write-Ahead Log)**<br>
`TestWALAppendAndReplay`<br>
Validates WAL append correctness and confirms that replaying the log reconstructs the exact write sequence.<br>

**4. Memtable Semantics**<br>
`TestMemtablePutAndGet`<br>
Checks basic insert and lookup functionality within the active memtable.<br>

`TestMemtableOverwriteBySeq`<br>
Ensures that for the same key, entries with higher sequence numbers override older versions.<br>

`TestMemtableTombstone`<br>
Validates correct handling of tombstones in the memtable, ensuring deletes are respected.<br>

**5. Flush & SSTable Creation**<br>
`TestMemtableFlushCreatesSSTable`<br>
Confirms that flushing a full memtable produces a valid immutable SSTable on disk.<br>

`TestSSTableWriteAndGet`<br>
Validates SSTable encoding and lookup logic, ensuring persisted data can be read back correctly.<br>

**6. Read Path & Shadowing Rules**<br>
`TestReadActiveMemtableShadowsSSTable`<br>
Ensures that newer data in the active memtable takes precedence over older SSTable entries.<br>

`TestReadNewerSSTableShadowsOlder`<br>
Validates correct shadowing when multiple SSTables exist, ensuring newer tables override older ones.<br>

`TestReadTombstoneShadowsAll`<br>
Confirms that tombstones suppress values from both memtables and SSTables.<br>

`TestReadMissingKey`<br>
Ensures reads for non-existent keys return a consistent “not found” result.<br>

`TestReadFromActiveMemtable`<br>
Checks that reads correctly return data from the active memtable before consulting SSTables.<br>

`TestReadAfterFlush`<br>
Ensures that data remains readable after a memtable flush, validating the durability boundary.<br>

`TestReadWithTombstone`<br>
Confirms that deleted keys remain logically deleted even after flush and recovery.<br>

---

## Test Results:

```golang
=== RUN   TestEngineCrashRecoveryFromWAL
--- PASS: TestEngineCrashRecoveryFromWAL (0.01s)
=== RUN   TestEngineCrashRecoveryWithDelete
--- PASS: TestEngineCrashRecoveryWithDelete (0.01s)
=== RUN   TestPhase0EngineOpenCreatesDirectories
--- PASS: TestPhase0EngineOpenCreatesDirectories (0.00s)
=== RUN   TestEnginePutAndDelete
--- PASS: TestEnginePutAndDelete (0.01s)
=== RUN   TestEngineSequenceIncrements
--- PASS: TestEngineSequenceIncrements (0.02s)
=== RUN   TestMemtableFlushCreatesSSTable
--- PASS: TestMemtableFlushCreatesSSTable (0.02s)
=== RUN   TestMemtablePutAndGet
--- PASS: TestMemtablePutAndGet (0.00s)
=== RUN   TestMemtableOverwriteBySeq
--- PASS: TestMemtableOverwriteBySeq (0.00s)
=== RUN   TestMemtableTombstone
--- PASS: TestMemtableTombstone (0.00s)
=== RUN   TestReadActiveMemtableShadowsSSTable
--- PASS: TestReadActiveMemtableShadowsSSTable (0.03s)
=== RUN   TestReadNewerSSTableShadowsOlder
--- PASS: TestReadNewerSSTableShadowsOlder (0.03s)
=== RUN   TestReadTombstoneShadowsAll
--- PASS: TestReadTombstoneShadowsAll (0.02s)
=== RUN   TestReadMissingKey
--- PASS: TestReadMissingKey (0.00s)
=== RUN   TestReadFromActiveMemtable
--- PASS: TestReadFromActiveMemtable (0.01s)
=== RUN   TestReadAfterFlush
--- PASS: TestReadAfterFlush (0.01s)
=== RUN   TestReadWithTombstone
--- PASS: TestReadWithTombstone (0.01s)
=== RUN   TestRecoveryIsIdempotent
--- PASS: TestRecoveryIsIdempotent (0.02s)
=== RUN   TestSSTableWriteAndGet
--- PASS: TestSSTableWriteAndGet (0.01s)
=== RUN   TestWALAppendAndReplay
--- PASS: TestWALAppendAndReplay (0.01s)
PASS
ok      vern_kv/tests   0.232s
```
