## 1. Identity

| Field | Value |
|---|---|
| Name | LVE |
| Repository Type | Library |
| Classification basis | `.ai/repository.yml` declares `type: Library` |
| Standards | knowledge-contract-v1, repository-classification-v1 |
| Component type | ADempiere Java localization library |
| Language and target | Java 17 (`sourceCompatibility = 1.17`; CI uses Temurin JDK 17) |
| Build / runtime | Gradle 7.3.3 wrapper |
| Published artifact | `io.github.adempiere:location-venezuela:<version>` |
| Version | `deployVersion`, `ADEMPIERE_LIBRARY_VERSION`, or fallback `local-1.0.0` |
| License | README states GNU/GPLv2 or later; build POM declares GNU General Public License v3.0; source headers vary between GPL-2 and GPL-3 |
| Root package or module | `org.erpya.lve` |
| Owner | ERP Consultores y Asociados |
| Upstream | `https://github.com/adempiere/LVE` |
| Default branch | `erpya` |

## 2. Responsibility

This repository owns the Venezuelan localization for ADempiere: IVA, ISLR, and municipal withholding; fiscal document control numbers and validations; bank payment export files and bank statement imports for Venezuelan banks; payroll export files; tax discount / special-tax recalculation; automatic invoice allocation and overpayment handling; FBTT (Financial Big Transaction Tax); fiscal books and withholding reports; and the dictionary migrations, SQL views/functions, and `es_MX`/`es_VE` translations that install this capability.

It depends on the forked `withholding-engine` for the generic withholding document engine. It does not own the ADempiere core, the generic withholding engine, store/POS core logic, or customer-specific customizations. It is a reusable Library; it is not the UI layer, and it is not a `PatchCustomer`.

## 3. Architecture

The repository is a Gradle Java library built from four source roots:

```text
core/
  src/main/java/base/org/erpya/lve/
    model/           model validators, interface/model classes, generated X_ classes
    process/         fiscal document and shipment processes
    setup/           setup definitions that register model validators
    util/            control numbers, document allocations, organization rules
financial/
  src/main/java/base/org/erpya/lve/
    bank/exp/        bank payment export formats
    bank/imp/        bank statement import loaders/transactions
    bank/matcher/    bank statement payment matchers
    model/           FBTT model validator
    process/         FBTT process
    setup/           FBTT setup
human_resource/
  src/main/java/base/org/erpya/lve/
    bank/exp/        payroll bank payment exports
    util/            Cesta Ticket, BANAVIH, TodoTicket payroll exports
withholding/
  src/main/java/base/org/erpya/lve/
    model/           withholding model classes
    process/         AR withholding process
    setup/           POS IVA setup
    util/            IVA/ISLR/Municipal withholding implementations and POS references
    util/exp/        report export formats
xml/
  migration/         155 dictionary migration XML files
  report/            report definition
  translation/es_MX/ translation seeds
  translation/es_VE/ translation seeds
db/ddlutils/
  oracle/views/      fiscal day summary view
  postgresql/
    functions/       price-list and tax-rate functions
    views/           invoice, withholding, and fiscal book views
```

Key extension points:

- ADempiere model validators implemented under `org.erpya.lve.model` and `org.erpya.lve.model.validator`, registered through `org.erpya.lve.setup` setup classes.
- Bank payment exporters extend `LVEPaymentExportList`.
- Bank statement importers extend `org.spin.util.impexp.BankStatementHandler` and `BankTransactionAbstract`; matchers implement `BankStatementMatcherInterface`.
- Withholding implementations extend `AbstractWithholdingSetting`.
- Reports may add export formats registered in `WithholdingReportExport`.

Generated code under `X_*`, `I_*`, and `*Abstract` must not be edited by hand.

### Pre-existing records this repository modifies

The evidence `UPDATES TO PRE-EXISTING RECORDS` table lists every pre-existing record this repository updates. The rows below identify the modified records. The AD_Field list is large and is shown in a grouped form because every row contains the same kind of update (`SeqNo`) across the same migration family.

| Record | Table | Columns changed | Effect | Migration |
|---|---|---|---|---|
| `53399, 53400` | AD_Element | PO_Description, PO_Help, PO_Name, AD_Reference_Value_ID, Help, Description, PO_PrintName | Updates base element naming/translation for LVE windows | `0002` |
| `2901, 2909` | AD_Column | Callout | Attaches `org.erpya.lve.model.CalloutBPartner.taxI` to BPartner TaxID/Value columns | `0002` |
| `100` | AD_PrintPaper | Description, Name | Reconfigures print paper for employee verification report | `0073` |
| `133` | AD_PrintFont | IsDefault | Sets font default for employee verification report | `0073` |
| `1000005` | AD_Process | JasperReport | Changes payroll report path | `2040` |
| `195, 53552` | AD_Window | AD_Image_ID | Clears/updates window image reference | `2030` |
| `53626, 181, 54422, 426, 53253, 537, 311, 292, 504, 515` | AD_TreeNodeMM | Node_ID, SeqNo, AD_Tree_ID | Reorders menu tree nodes | `0030`, `1080`, `3070` |
| `153, 263, 166, 203, 53242, 236, 183, 160, 278, 345, 53207, 53014, 53363, 53229, 54649, 54465, 53066, 53108, 54269, 54813` | AD_TreeNodeMM | Node_ID, SeqNo, AD_Tree_ID | Reorders menu tree nodes | `1080`, `3070` |
| See AD_Field list below | AD_Field | SeqNo; a few also UUID | Renumbers fields across LVE-related windows | `0002`, `0007`, `0014`, `0024`, `0027`, `0028`, `0029`, `0030`, `0040`, `0065`, `0072`, `0089`, `2030` |

The AD_Field Record_IDs modified include:

`2877, 1110, 1114, 10124, 55410, 55411, 55412, 1108, 1109, 2878, 56446, 1107, 1104, 1077, 1103, 8653, 1098, 3272, 2112, 2109, 3113, 1099, 56906, 2593, 2589, 1324, 7038, 7826, 7825, 58194, 58195, 1112, 1113, 1082, 1084, 6560, 1083, 58037, 3660, 52014, 3451, 10123, 55413, 55414, 55415, 3444, 3447, 3464, 3443, 3448, 3420, 3441, 8652, 3438, 3467, 3456, 3454, 3466, 3439, 3459, 3457, 3446, 7039, 7824, 7823, 58210, 58211, 3425, 3427, 3449, 3450, 6506, 3426, 3671, 2775, 2764, 2768, 6935, 7794, 7795, 58220, 58221, 2786, 2780, 2778, 2771, 8657, 10485, 6564, 2777, 3663, 3899, 13700, 53257, 53258, 4246, 4247, 3332, 3323, 3327, 6936, 7796, 7797, 58226, 58227, 3343, 3337, 3335, 3329, 10486, 6532, 200048, 3334, 3670, 3900, 86221, 3073, 807, 808, 54233, 54230, 82920, 6567, 3125, 63065, 54232, 83990, 10345, 10346, 10481, 10480, 58859, 10371, 10528, 10340, 3075, 910, 911, 904, 5346, 4727, 905, 1309, 1310, 1311, 1312, 3052, 3054, 9201, 1564, 1563, 1562, 1565, 1566, 1567, 10319, 10320, 57531, 57534, 57535, 2959, 2954, 6565, 2958, 2776, 2766, 2767, 2765, 2961, 2770, 8648, 2763, 3273, 2953, 2956, 3112, 2774, 3352, 3338, 3344, 3348, 3333, 3325, 3326, 3324, 3350, 3328, 8649, 3322, 3355, 3354, 3346, 3353, 3331, 12746, 13660, 13662, 13663, 13661, 13692, 13666, 13667, 58260, 58261, 5819, 2995, 8243, 8265, 12747, 13668, 13670, 13671, 13669, 13693, 13674, 13675, 74297, 58264, 58265, 3370, 8266, 8244, 8267, 76161, 76162, 76164, 76165, 93656, 1314, 1315, 8728, 56622, 58855, 58852, 58854, 58853, 56623, 56621, 56624, 57532, 3071, 12745, 13644, 13646, 13647, 13645, 13691, 13650, 13651, 58201, 58202, 69922, 69347, 69996, 2880, 12744, 10332, 7247, 7224, 7831, 7829, 7830, 7832, 7828, 7827, 58229, 58230, 2707, 10369, 9463, 9462, 3280, 3281, 10568, 5143, 200050, 86282, 93655, 95808, 95809, 3482, 10347, 9236, 9235, 3505, 3506, 10531, 5146, 57733, 57734, 57735, 57736, 57737, 57738, 57739, 57740, 57741, 57742, 57743, 57744, 57745, 57746, 57747, 57748, 57749, 57750, 57751, 57752, 57871, 57872, 57873, 57874, 57875, 57876, 57877, 57878, 57879, 57880, 57881, 57882, 57883, 57884, 57885, 57886, 57887, 57888, 57889, 57890, 57891, 10902, 6551, 6969, 11013, 7809, 7807, 7808, 7806, 7810, 58267, 58268, 99783, 99663, 99676, 4133, 4129, 8651, 4131, 5117, 5736, 5737, 4056, 4363, 4054, 4027, 4032, 4041, 4036, 4057, 4035, 4037, 4033, 4034, 4023, 4025, 4019, 4026, 4024, 6299, 4021, 4022, 4020, 4055, 4043, 4058, 4042, 4258, 4039, 4053, 4052, 4051, 4047, 4049, 4048, 4362, 4361, 85505, 6552, 4044, 4266, 96849, 52052, 95804, 84135, 84136, 84137, 84138, 84139, 84140, 84141, 84142, 84143, 84144, 84145, 99784, 99664, 99677, 84146, 84147, 84148, 84149, 84150, 84151, 84152, 84153, 84154, 84155, 84156, 84157, 84158, 84159, 84160, 84161, 84162, 84163, 84164, 84165, 84166, 84167, 84168, 84169, 84170, 84171, 84172, 84173, 84174, 84175, 84176, 84177, 84178, 84179, 84180, 84181, 84182, 84183, 84184, 84185, 84186, 84187, 84188, 96850, 84189, 84190, 84191`

The full evidence file lists the exact migration for each row.

## 4. Dependencies

| Dependency | Scope | Purpose |
|---|---|---|
| `io.github.adempiere:human-resource-and-payroll:3.9.4` | compile / api | ADempiere HR and payroll model classes used by payroll exports |
| `io.github.adempiere:store:3.9.4` | compile / api | ADempiere store/POS classes used by POS withholding |
| `io.github.adempiere:withholding-engine:adempiere-3.9.4-1.3.6` | compile / api | Fork of the withholding engine; must resolve from `erpcya/withholding-engine`, not central |
| `javax.mail:javax.mail-api:1.6.2` | compile / api | Email handling/validation in bank exports |
| `xml-apis:xml-apis:2.0.2` | compile / api | XML parsing for ISLR export |
| `io.vavr:vavr:0.10.4` | compile / api | Functional utilities |
| `lib/*.jar` | compile / api fileTree | Local jar dependencies; no `lib/` directory is present in tracked files |
| Gradle wrapper 7.3.3 | build | Build tool |
| Java 17 Temurin | build / runtime | CI and runtime target |
| GitHub Maven Packages `erpcya/withholding-engine` | build | Forked dependency repository; credentials supplied by CI secrets |
| Maven publish repositories | publish | Sonatype/GitHub publication targets; credentials supplied by `ORG_GRADLE_PROJECT_deploy*`/workflow secrets |

## 5. Consumers

- ADempiere installations install `LVE.jar` into `$ADEMPIERE_HOME/packages/LVE/lib`, import the XML migrations and translations from the release package.
- Maven consumers depend on `io.github.adempiere:location-venezuela:<version>`.
- ADempiere dictionary processes, reports, model validators, and windows reference Java classes and report definitions shipped by this library.
- Release evaluation and downstream platform agents read the migration files and the pre-existing record modifications they contain.

Changing migration sequence numbers, migration filenames, published artifact coordinates, process/report class names, model validator class names, SQL view/function names, or the package `org.erpya.lve` can break these consumers even when compilation still succeeds.

## 6. Allowed changes

- Add new dictionary migrations under `xml/migration/` with unique, sequential filenames and `SeqNo`; use `EntityType` `LVE` unless a deliberate external module reference is documented.
- Add or modify model validators and registration classes under `org.erpya.lve.model` / `org.erpya.lve.setup`.
- Add new bank payment export or statement import classes extending the existing base classes.
- Add new report export formats and register them in `WithholdingReportExport`.
- Extend SQL functions/views under `db/ddlutils/` and update the matching dictionary view definitions in migrations.
- Update `xml/translation/es_MX` and `xml/translation/es_VE` when dictionary records change.
- Adjust Gradle/Java toolchain when CI and runtime are updated together.
- Release new versions by setting `deployVersion` or `ADEMPIERE_LIBRARY_VERSION`, never by changing the artifact identity.

## 7. Prohibited changes

- Do not hand-edit generated code:
  - `X_*` model classes
  - `I_*` interfaces
  - `*Abstract` process classes
- Do not change the published coordinates `io.github.adempiere:location-venezuela`.
- Do not delete or rename already-shipped migration XML files; consumers rely on the import sequence.
- Do not introduce customer-specific behavior into this reusable Library.
- Do not add a dependency on `PatchCustomer`.
- Do not commit secrets, credentials, tokens, signing keys, or publish passwords. They belong in GitHub Actions secrets or environment variables referenced by the existing workflows.
- Do not keep `.classpath`, `.project`, or `.settings/` in version control; remove them and ignore them.
- Do not change fixed-width/separator bank contracts without testing the generated files against the affected bank’s specification.
- Do not perform direct Base modifications from this repository; it is a Library build that depends on the Base, not a Base repository.

## 8. Architectural rules

1. Dictionary changes are delivered only through `xml/migration/*.xml` and must be importable by ADempiere in filename/`SeqNo` order.
2. Java code remains under `org.erpya.lve` and is placed in one of the four source sets by responsibility: `core`, `financial`, `human_resource`, `withholding`.
3. Generated classes are never edited by hand.
4. Bank exporters must extend `LVEPaymentExportList`; importers must fit the `BankStatementHandler`/`BankTransactionAbstract` model; matchers must implement `BankStatementMatcherInterface`.
5. Model validators are registered through setup classes implementing `ISetupDefinition` and must set `EntityType` to `LVE`.
6. Process/report values, class names, migration names, and SQL object names referenced by the dictionary must remain stable between released versions.
7. SQL DDL files and migration view/function definitions must agree.
8. The published artifact must remain independent of customer-specific repositories.

## 9. Risks

| Check | Finding | Impact | Precaution |
|---|---|---|---|
| Identifiers outside the allowed allocation range | No allocation range is declared by this repository’s evidence. Inserted `Record_ID` values are 1–6 digits; the only observed 7-digit ID, `1000005`, is an update to a pre-existing `AD_Process`. | Allocation compliance cannot be verified from this repository alone. | Obtain the approved identifier allocation range from the platform owner and record it in future contracts. |
| Build output or IDE metadata under version control | `.classpath`, `.project`, and `.settings/` are tracked. | Machine-specific Eclipse files cause dirty checkouts and import conflicts. | Remove these files from version control and add them to `.gitignore`. |
| Secrets in the tree or recoverable from history | `None found`. Workflow and build files reference credential-bearing keys without values; the collector masks them by key name. | No current exposure identified. | Keep credentials in GitHub secrets/env and never interpolate them into tracked files. |
| Absent verification mechanism | No test sources or test framework are present; CI runs only `./gradlew build`. | Tax, bank export, and fiscal validation regressions can reach releases undetected. | Use the release-candidate/verified workflow and manual functional testing for bank formats and fiscal validators. |
| Pre-existing records modified (cross-reference section 3) | Yes: `AD_Element`, `AD_Column`, many `AD_Field` `SeqNo` changes, `AD_TreeNodeMM`, `AD_PrintPaper`, `AD_PrintFont`, `AD_Process`, and `AD_Window` rows. | Release can alter existing user windows, menus, field order, and reports on installations that already have these records. | Review the pre-existing record updates in every release before shipping; treat menu/window renumbering as user-visible. |

| Risk | Impact | Precaution |
|---|---|---|
| Duplicate migration filenames and `SeqNo` values | There are duplicate filenames such as two `0030` files, two `0093` files, two `0102`, two `1019`, and two `0028` files. Import tooling may resolve the wrong file or report ambiguity. | Audit `xml/migration/` and assign unique filenames/`SeqNo` before next release. |
| Entity type anomalies in migrations | Some inserted records carry `WHH`, `EE02`, `ECA02`, or `ECA42` instead of `LVE`. They may be deliberate references to other modules, but they currently look like typos. | Review the `ENTITY TYPE ANOMALIES` list; correct to `LVE` or document the deliberate module reference. |
| License inconsistency | README says GPLv2 or later; build POM declares GPL-3.0; source headers vary between GPL-2 and GPL-3. | Align README, POM, `LICENSE`, and source headers with the owner’s intended license. |
| Upstream fork reconciliation | Local changes are a merge cost on every upstream update; upstream merge bulk is not itself a release. | Keep upstream `master` untouched and work on the `erpya` default branch; minimize changes to upstream files. |
| `lib/*.jar` references with no tracked `lib/` directory | `build.gradle` declares `api fileTree(dir: 'lib', include: ['*.jar'])`; no `lib/` directory appears in the tracked file list. | Verify whether CI/publish builds rely on untracked local jars; add the jars to VCS or remove the dependency block. |
| `.ai/repository.yml` stale comment | The file’s comment says it is “Declarative, not authoritative,” while the current classification standard says the type in the file is authoritative. | Update the repository marker comment to match the classification standard. |
| `sourceCompatibility = 1.17` | This is not the standard Gradle spelling for Java 17, though the build may still work. | Confirm with Gradle toolchain and CI; consider `JavaVersion.VERSION_17` if needed. |

## 10. Current state

The repository is a forked ADempiere Library with default branch `erpya`; upstream is preserved as `master`. The Gradle build targets Java 17 and publishes `location-venezuela` under `io.github.adempiere`. CI builds on pull requests/branches and publishes on release; an ERP AI release-candidate workflow is also present.

It currently provides:

- IVA, ISLR, and municipal withholding for AP/AR documents and POS orders.
- Venezuelan bank payment export classes for multiple banks, including versioned variants for Banesco, BNC, Mercantil, Bancaribe, Venezuela, and others.
- Bank statement importers for BNC, Bancaribe, Banplus, Mercantil, Provincial, Tesoro, and Venezuela formats.
- Bank matchers by currency/amount/date and direct debit.
- Payroll exports for Mercantil, Banesco, Venezuela, BOD, BNC, Banplus, Cesta Ticket, BANAVIH, and TodoTicket.
- Fiscal document control number generation, printing, reprint authorization, shipment note generation, invoice cancellation, and fiscal void/voiding validations.
- SQL views for fiscal books, withholding, and invoice day summary.
- `es_MX` and `es_VE` translation seed files.

Known boundaries and defects:

- No automated test suite is present.
- Eclipse project metadata is tracked.
- Migration filenames/`SeqNo` are not globally unique.
- Some migrations insert records with entity types other than `LVE`.
- The intended license is inconsistent across README, POM, and source headers.

## 11. UNKNOWN

- The approved ADempiere `Record_ID` allocation range for this repository; verify with dictionary allocation authority.
- Whether the entity type anomalies `WHH`, `EE02`, `ECA02`, `ECA42` are deliberate external module references or typos; verify with the owners of those modules.
- Whether `.classpath`, `.project`, and `.settings/` are required by any team workflow or can be removed; verify with maintainers.
- The intended final license version; verify with the repository owner.
- Whether `sourceCompatibility = 1.17` is intentional and valid for the current Gradle/Java toolchain; verify with a clean build.
- Which local `lib/*.jar` files the `fileTree` dependency expects, and whether they must be published or added to version control; verify with the build owner.
- Which duplicate migration file should be considered canonical and whether all duplicates have ever been applied successfully; verify import logs.
- The full set of AD_Field `SeqNo` changes should be reviewed against functional window tests; whether every pre-existing field renumbering is intended remains unverified.