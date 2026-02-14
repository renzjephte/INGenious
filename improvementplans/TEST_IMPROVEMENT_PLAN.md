# INGenious — Unit Test Improvement Plan

**Created:** February 13, 2026  
**Current State:** 8 test files / 537 source files = 1.5% test coverage  
**Goal:** Achieve meaningful coverage of core, testable logic with a phased approach  

---

## Current Baseline

| Module | Source Files | Test Files | Coverage | Tests |
|--------|------------|------------|----------|-------|
| **Engine** | 215 | 7 | ~0% | 10 methods (3 disabled) |
| **IDE** | 212 | 0 | 0% | — |
| **Datalib** | 82 | 1 | ~1% | 8 methods |
| **StoryWriter** | 23 | 0 | 0% | — |
| **TestData-Csv** | 4 | 0 | 0% | — |
| **Common** | 1 | 0 | 0% | — |

### Key Problems
- Most HTTP tests are **disabled** (`enabled = false`)
- Tests are mostly trivial (getter/setter, string constants)
- No mocking framework (no Mockito)
- No assertion library beyond basic TestNG asserts
- Raw types in test code

---

## Prerequisites

Before writing tests, add Mockito and AssertJ to the parent POM:

```xml
<!-- In parent pom.xml <properties> -->
<mockito.version>5.14.2</mockito.version>
<assertj.version>3.26.3</assertj.version>

<!-- In parent pom.xml <dependencyManagement> -->
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>${mockito.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>${assertj.version}</version>
    <scope>test</scope>
</dependency>
```

---

## Sprint 1 — Pure Logic & Data Models (Week 1-2)

**Target: ~45 test classes | Effort: 5-6 days | Expected coverage lift: +8-12%**

These classes have zero external dependencies — pure POJOs, enums, utilities, and collection logic. Maximum ROI.

### 1.1 Datalib Model Classes (9 classes → 9 test classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `AttributeTest` | `Attribute` | Factory method, getters, JSON serialization |
| `AttributesTest` | `Attributes` | `add`, `find`, `update`, `contains`, stream logic |
| `DataTest` | `Data` | List wrapper operations |
| `DataItemTest` | `DataItem` | Constructor, getters, tag association |
| `MetaTest` | `Meta` | Constructor, attribute/tag management |
| `MetaListTest` | `MetaList` | `filter`, `findByTypeName`, collection operations |
| `ProjectInfoTest` | `ProjectInfo` | Getters, nested object graph |
| `TagTest` | `Tag` | Value getter/setter |
| `TagsTest` | `Tags` | Collection operations |

### 1.2 Datalib OR Classes (2 testable classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ORAttributeTest` | `ORAttribute` | Constructor, clone, getters/setters |
| `ObjectGroupTest` | `ObjectGroup` | TreeNode operations, child management |

### 1.3 Datalib Component Data Classes (2 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ExecutionStepTest` | `ExecutionStep` | HEADERS enum values, data field get/set |
| `TestStepTest` | `TestStep` | HEADERS enum, step data parsing |

### 1.4 Datalib Settings — Pure Logic (2 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `RunSettingsTest` | `RunSettings` | Typed getters (string→int, string→bool), defaults |
| `ExecutionSettingsTest` | `ExecutionSettings` | Composition, getter delegation |

### 1.5 Engine Execution Data — Pure Logic (4 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ParameterTest` | `Parameter` | `resolveMaxIter`, `resolveStartIter` — regex parsing |
| `DataIteratorTest` | `DataIterator` | Iteration counting, max computation |
| `StepSetTest` | `StepSet` | Loop from/to, counter increment, break flag |
| `DataProcessorStaticTest` | `DataProcessor` | `trimFirst`, `isInputPatternDynamic`, `isInputPatternDataSheet` |

### 1.6 Engine Support & Constants (3 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `StatusTest` | `Status` | Enum `toString`, `getValue`, all variants |
| `StepTest` | `Step` | `create()` factory methods |
| `SystemDefaultsExpTest` | `SystemDefaults` | Expand existing test: debug flag, wait times |

### 1.7 Engine Utilities (2 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `KeyMapExpandedTest` | `KeyMap` | Expand existing: nested vars, edge cases, missing keys, empty maps |
| `StringOpsStaticTest` | `StringOperations` | `isNumeric`, `countCharOccurrences` static methods |

### 1.8 Engine Driver Enum (1 class)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `BrowserEnumTest` | `PlaywrightDriverFactory.Browser` | `fromString`, `getValuesAsList` |

### 1.9 StoryWriter Utilities (5 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ValidatorTest` | `Validator` | `isValidName` — valid/invalid filenames, edge cases |
| `DSTest` | `DS` | `update`, `updateV`, step type constants |
| `StoryTest` | `Story` | `toJSON`, `update`, string parsing |
| `ToolsDateTest` | `Tools` | `today`, `after(days)`, `toDate`, `getMillisNow` |
| `UtilityTest` | `Utility` | `isEmpty`, `getValue`, `getDays`, date formatting |

### Sprint 1 Exit Criteria
- ~45 new test classes
- All pure-logic methods covered
- JaCoCo coverage ≥ 10% overall
- Zero test failures in CI

---

## Sprint 2 — Settings & File-Backed Classes (Week 3-4)

**Target: ~20 test classes | Effort: 4-5 days | Expected coverage lift: +5-8%**

Uses temp directories and files. Add `@TempDir` (TestNG equivalent or JUnit-style) for filesystem isolation.

### 2.1 Datalib Settings with File I/O (12 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `AbstractPropSettingsTest` | `AbstractPropSettings` | Load/save properties files in temp dir |
| `PropUtilsTest` | `PropUtils` | `loadProperties`, `saveProperties` with temp files |
| `CapabilitiesTest` | `Capabilities` | Load per-browser `.properties` from temp folder |
| `ContextOptionsTest` | `ContextOptions` | Named option set CRUD |
| `DBPropertiesTest` | `DBProperties` | Multi-DB property file management |
| `DriverPropertiesTest` | `DriverProperties` | API config loading |
| `EmulatorsTest` | `Emulators` | JSON load/save with Jackson |
| `MailSettingsTest` | `MailSettings` | Default values, load/save |
| `TestMgmtModuleTest` | `TestMgmtModule` | JSON module definition parsing |
| `LambdaTestCapsTest` | `LambdaTestCaps` | Property defaults, load/save |
| `UserDefinedSettingsTest` | `UserDefinedSettings` | Custom property handling |
| `ProjectSettingsTest` | `ProjectSettings` | Composite settings initialization |

### 2.2 TestData-Csv Module (4 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `CSVUtilsTest` | `CSVUtils` | `load`, `loadColumns`, `saveChanges` with temp CSV files |
| `CsvDataProviderTest` | `CsvDataProvider` | Data loading with mocked `Project` |
| `CsvGlobalDataTest` | `CsvGlobalData` | Global data model operations |
| `CsvTestDataTest` | `CsvTestData` | Sheet data model operations |

### 2.3 StoryWriter File-Based Classes (3 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ToolsFileTest` | `Tools` | `readFile`, `writeFile` with temp files |
| `StoryParserTest` | `StoryParser` | Parse sample `.feature` files |
| `BDDProjectTest` | `BDDProject` | Load/save BDD project JSON |

### Sprint 2 Exit Criteria
- ~20 new test classes
- All settings classes tested with temp file I/O
- CSV data provider fully tested
- JaCoCo coverage ≥ 18% overall

---

## Sprint 3 — Mocked Integration Logic (Week 5-7)

**Target: ~15 test classes | Effort: 5-7 days | Expected coverage lift: +5-8%**

Requires Mockito for mocking `CommandControl`, `TestCaseRunner`, HTTP clients, etc.

### 3.1 Engine Data Access (3 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `DataProcessorResolveTest` | `DataProcessor` | `resolve(raw, context, subIter)` with mocked runner |
| `DataAccessTest` | `DataAccess` | `getData`, `putData` with mocked context |
| `DataAccessInternalTest` | `DataAccessInternal` | Iteration helpers with mocked `TestDataModel` |

### 3.2 Engine HTTP/Reporting (3 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `BasicHttpClientMockTest` | `BasicHttpClient` | Replace disabled tests with mocked HTTP; test auth, proxy, SSL config |
| `DefectModulesTest` | `DefectModules` | `getDecoded`, `checkServer` logic |
| `UnknownSyncTest` | `Unknown` | No-op contract verification |

### 3.3 Engine Webservice (1 class — multiple test classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `WebserviceRequestMethodTest` | `Webservice` | `RequestMethod` enum |
| `WebserviceJsonPathTest` | `Webservice` | JSONPath extraction with mocked `CommandControl` |
| `WebserviceXPathTest` | `Webservice` | XPath extraction logic |
| `WebserviceHttpTest` | `Webservice` | HTTP call construction with mocked `HttpClient` |

### 3.4 Engine String Operations (1 class)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `StringOperationsActionTest` | `StringOperations` | `@Action` methods with mocked `CommandControl` |

### 3.5 Engine Core (3 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `RunContextTest` | `RunContext` | Context data management |
| `ThreadPoolTest` | `ThreadPool` | Error handling in `afterExecute` |
| `TMIntegrationTest` | `TMIntegration` | Factory logic, `decrypt` method |

### 3.6 Datalib Component Classes (4 classes)

| Test Class | Source Class | What to Test |
|------------|-------------|-------------|
| `ScenarioTest` | `Scenario` | Test case management, child node logic |
| `TestCaseTest` | `TestCase` | Step ordering, save listeners |
| `ReleaseTest` | `Release` | Test set management |
| `TestSetTest` | `TestSet` | Execution step management |

### Sprint 3 Exit Criteria
- ~15 new test classes
- Core data access pipeline tested
- Webservice command logic tested
- HTTP client properly tested (replacing disabled tests)
- JaCoCo coverage ≥ 25% overall

---

## Sprint 4 — Engine Commands & Driver Logic (Week 8-10) ✅ COMPLETED

**Target: ~10 test classes | Effort: 4-6 days | Expected coverage lift: +3-5%**
**Result: 10 test classes | ~132 new tests | Engine: 274 → 406 tests | Total: 841 → 973 tests | BUILD SUCCESS**

Focus on the isolatable logic within browser/mobile command classes using heavy mocking.

### 4.1 Browser Command Testable Logic (5 classes) ✅

| Test Class | Source Class | What to Test | Tests |
|------------|-------------|-------------|-------|
| `DynamicObjectTest` ✅ | `DynamicObject` | `setProperty` map manipulation via mock(CALLS_REAL_METHODS) + reflection | 10 |
| `PerformanceTest` ✅ | `Performance` | `escapeName` via reflection + DataProvider (special chars, null, collapse) | 11 |
| `RequestFulfillTest` ✅ | `RequestFulfill` | Static Command maps: mockEndPoints, responsebodies, responsecodes, headers, urlParams | 9 |
| `CookiesTest` ✅ | `Cookies` | `storeCookiesInVariable` formatting, `clearCookies` delegation | 5 |
| `StorageStateTest` ✅ | `StorageState` | `StoreStorageState` BrowserContext delegation, error reporting | 2 |

### 4.2 Engine Driver Logic (3 classes) ✅

| Test Class | Source Class | What to Test | Tests |
|------------|-------------|-------------|-------|
| `ChromeEmulatorsExpandedTest` ✅ | `ChromeEmulators` | `getPrefLocation`, JSON serialization round-trip, list manipulation, Preferences parsing | 10 |
| `PlaywrightDriverFactoryTest` ✅ | `PlaywrightDriverFactory` | `getPropertyValueAsDesiredType` (Boolean/Double/String), viewport, geolocation, screen/video size | 15 |
| `AutomationObjectLocatorTest` ✅ | `AutomationObject` | FindType enum, chainLocatorMapping, getRuntimeValue, storeElementDetailsinOR, getAttributeValue, waitTime | 25 |

### 4.3 Engine Utility (2 classes) ✅

| Test Class | Source Class | What to Test | Tests |
|------------|-------------|-------------|-------|
| `AppResourcePathTest` ✅ | `AppResourcePath` | All ~30 static path construction methods, consistency validation | 30 |
| `AnnontationUtilTest` ✅ | `AnnontationUtil` | `detect()` with MockedStatic\<AppResourcePath\>, external commands dir, multiple packages | 4 |

### Sprint 4 Exit Criteria ✅
- ✅ 10 new test classes (target: ~10)
- ✅ Core command logic tested where isolatable
- ✅ BUILD SUCCESS, 0 failures across all 973 tests

---

## Ongoing — Expand & Maintain

After the 4 sprints, shift to a **test-with-every-PR** model:

| Practice | Description |
|----------|-------------|
| **Coverage gate** | Set JaCoCo minimum at achieved baseline (e.g., 28%); ratchet up 2% quarterly |
| **New code rule** | Every new class or method must include tests; enforce via PR review |
| **Bug-driven tests** | Write a failing test for every bug before fixing it |
| **Flaky test triage** | Monitor CI for flaky tests weekly; fix or quarantine |
| **Integration tests** | Eventually add Playwright-based integration tests (separate module, separate CI job) |

---

## Summary Timeline

```
Sprint 1 (Wk 1-2)  ████████ Pure logic & data models       (~45 tests, +10% cov) ✅ 26 classes / 429 tests
Sprint 2 (Wk 3-4)  ████████ Settings & file-backed classes  (~20 tests, +7% cov)  ✅ 18 classes / 178 tests
Sprint 3 (Wk 5-7)  ████████████ Mocked integration logic    (~15 tests, +7% cov)  ✅ 18 classes / 234 tests
Sprint 4 (Wk 8-10) ████████████ Commands & driver logic      (~10 tests, +4% cov) ✅ 10 classes / 132 tests
```

**Total: 72 new test classes, 973 total tests, BUILD SUCCESS, 0 failures**

### Priority Order Within Each Sprint
1. Classes with the most downstream dependents (e.g., `RunSettings`, `DataProcessor`, `KeyMap`)
2. Classes with complex logic (e.g., `Webservice`, `StringOperations`, `Parameter`)
3. Classes with recent bugs or changes
4. Simple POJOs (quick wins for coverage numbers)

### What NOT to Test (for now)
- IDE module (212 files) — heavily Swing-based, requires specialized UI testing frameworks
- Browser command classes that are 100% Playwright API calls
- `Control`, `RunManager`, `Task` — need architectural refactoring first to be testable
- Driver creation classes that launch real browsers
