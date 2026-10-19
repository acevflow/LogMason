# LogMason — Project Specification

**Project status:** Early development  
**Current target version:** `0.1.0`  
**Document status:** Initial specification; some technical decisions remain pending  
**License:** MIT

---

## 1. Project Overview

LogMason is an open-source Python logging library designed to provide an independent, configurable, and extensible logging system for Python applications.

The project aims to provide a reliable logging core with multiple loggers, shared configuration, local file persistence, exception handling, and custom fields. A separate terminal-based log viewer is planned for a future version.

LogMason is intended to be useful as a standalone library without requiring the future viewer to be installed or running.

### 1.1 Project Identity

| Property | Value | Status |
|---|---|---|
| Project name | LogMason | Provisional |
| Intended PyPI distribution name | `logmason` | Pending availability verification |
| Intended Python import package | `logmason` | Proposed |
| Project type | Python logging library | Defined |
| License | MIT | Accepted |
| Development status | Early development | Current |

The project and package names remain provisional until their availability and suitability for publication on PyPI have been verified.

The package must not be published to PyPI until it is sufficiently developed, tested, and prepared for release.

### 1.2 Project Goals

LogMason aims to:

- Provide an independent logging system for Python applications.
- Capture five standard logging severity levels.
- Support multiple independent logger instances.
- Provide global configuration through a manager.
- Allow individual loggers to override selected configuration options.
- Persist log records in local files.
- Support exception information and tracebacks.
- Support custom key-value fields.
- Provide a consistent implementation through both package installation and direct package copying.
- Establish an architecture that can support a separate terminal-based viewer in a future release.

### 1.3 Design Priorities

The project prioritizes a balance between:

- Functionality.
- Runtime performance.
- Ease of use.
- Development simplicity.
- Maintainability.
- Extensibility.

The initial implementation should remain as simple as possible while establishing a sound foundation for future development.

---

## 2. Project Philosophy

### 2.1 Independent Logging System

LogMason is intended to implement its own logging system rather than act merely as a wrapper around Python's standard `logging` module.

Full integration or compatibility with the standard logging system is outside the scope of version `0.1.0`.

The precise approach to future interoperability remains open.

### 2.2 Open Source

LogMason will be developed as an open-source project with a public repository under the MIT License.

Third-party code and dependencies must be reviewed for license compatibility before being incorporated.

### 2.3 Installation and Distribution

LogMason must support two intended usage methods:

1. Installation as a Python package, for example through `pip`.
2. Direct copying of the `logmason` package directory into another Python project.

Both methods must use the same implementation and public API for a given release. Separate implementations must not be maintained for the two distribution methods.

---

## 3. Accepted Project Decisions

The following decisions have been accepted during project planning.

### LM-DEC-001 — Separate Terminal Viewer

A textual log viewer running in a separate terminal is planned for a future version.

The viewer will be separate from the main application terminal and will provide commands for interacting with log records.

A graphical interface or full-screen terminal user interface is not planned for the initial viewer.

The viewer is excluded from version `0.1.0`.

### LM-DEC-002 — Five Logging Levels

LogMason will support the following five logging levels:

- `DEBUG`
- `INFO`
- `WARNING`
- `ERROR`
- `CRITICAL`

The core must capture records from all five levels without applying a minimum severity threshold.

Resource limits and behavior under saturation must be defined separately.

### LM-DEC-003 — Configurable Automatic Viewer Startup

When the separate terminal viewer is implemented, it should start automatically when logging is activated, with an option to disable automatic startup.

This behavior is deferred to a future version.

### LM-DEC-004 — Open-Source MIT License

LogMason will use the MIT License and maintain a public source repository.

Dependencies and third-party code must be reviewed for license compatibility.

### LM-DEC-005 — Version `0.1.0` Scope

The initial release is intended to cover the following areas:

- Logging core.
- Configuration.
- Local persistence.
- Exception handling.
- Custom fields.
- Distribution and import compatibility.
- Automated testing and quality assurance.

The viewer, interactive filtering, and viewer process communication are deferred.

The high-level scope has been selected, but the detailed functional specification and acceptance criteria still require confirmation.

### LM-DEC-006 — Global and Individual Configuration

A `Manager` maintains the global configuration.

Each `Logger` inherits global configuration values by default and may override selected options individually.

The following rules apply:

1. An individual override affects only the logger that owns it.
2. Changes to global configuration affect options that have not been individually overridden.
3. Removing an individual override restores inheritance from the current global value.
4. Individual configuration must not be shared implicitly between logger instances.

The complete list of configurable options and the allowed individual overrides remain to be finalized.

### LM-DEC-007 — Independent Logger Identity

Two `Logger` instances may have the same name while remaining independent objects.

Sharing a name does not imply shared identity, automatic instance reuse, or shared individual configuration.

Loggers associated with the same `Manager` share its global configuration.

Logger name hierarchy and parent-child configuration inheritance are not part of the confirmed initial design.

### LM-DEC-008 — Explicit Manager Requirement

Every `Logger` must receive a `Manager` explicitly.

A logger must not silently create or use an implicit default manager.

The intended usage pattern is:

```python
manager = Manager()

database_logger = Logger("database", manager)
api_logger = Logger("api", manager)
```

This example illustrates the intended API design; the final constructor signatures remain subject to confirmation.

### LM-DEC-009 — No Minimum Severity Filtering in the Core

The logging core must capture all five supported levels without applying a minimum severity threshold.

Severity filtering is reserved for the future viewer and is not part of the initial core configuration.

This decision does not guarantee unlimited storage or lossless operation under every possible resource failure.

---

## 4. Version `0.1.0` Scope

### 4.1 Included Areas

Version `0.1.0` is intended to include:

1. A functional logging core.
2. A `Manager` responsible for global configuration and shared resources.
3. Independent `Logger` instances.
4. The five supported logging levels.
5. Global configuration and individual overrides.
6. Local log-file persistence.
7. Exception and traceback recording.
8. Custom key-value fields.
9. Package distribution and direct-copy compatibility.
10. Automated tests and initial documentation.

The detailed behavior of these features must be finalized before the release scope can be considered closed.

### 4.2 Deferred Features

The following features are excluded from version `0.1.0`:

- Separate terminal log viewer.
- Automatic viewer startup.
- Interactive log filtering.
- Communication between the application and viewer processes.
- Graphical interfaces.
- Full integration with Python's standard `logging` module.
- Remote logging.
- Plugin architecture.
- Advanced configuration through external configuration files.
- Automatic log rotation.
- Guaranteed multi-process writes to the same log file.

These features may be considered for future releases. Their exclusion from `0.1.0` does not mean they are permanently rejected.

---

## 5. Architecture

### 5.1 Main Components

The proposed architecture has two principal classes: `Manager` and `Logger`.

#### Manager

The `Manager` is responsible for:

- Maintaining global configuration.
- Determining log-file locations.
- Managing persistence resources.
- Coordinating access to shared file resources.
- Managing resource initialization and shutdown.
- Providing global configuration to associated loggers.

#### Logger

Each `Logger` is responsible for:

- Maintaining its own name.
- Receiving an explicit `Manager`.
- Emitting records at the supported levels.
- Maintaining individual configuration overrides.
- Supporting exception information.
- Accepting custom fields.
- Applying its effective output format before persistence.

The exact division of responsibilities between the two classes remains subject to the final API and persistence design.

### 5.2 Conceptual Data Flow

The intended data flow is:

1. An application calls a method on a `Logger`.
2. The logger constructs a record containing the event information.
3. The effective configuration is applied.
4. The resulting representation is written to a local `.log` file.
5. The future viewer may consume or display log records through a separately designed mechanism.

The final step is not implemented in version `0.1.0`.

### 5.3 Logger Names

Logger names identify individual logger instances for record representation and identification.

Instances with identical names must remain independent unless an explicit future feature defines different behavior.

Automatic logger hierarchy, name-based instance reuse, and parent-child configuration inheritance are not currently specified.

---

## 6. Proposed Public API

The following API is a proposal and must not be treated as a finalized or implemented interface.

### 6.1 Logger Creation

The intended usage pattern is:

```python
from logmason import Manager, Logger

manager = Manager()

database_logger = Logger("database", manager)
api_logger = Logger("api", manager)
```

The explicit manager requirement is accepted. The exact constructors and their parameters remain to be finalized.

### 6.2 Logging Methods

The proposed public methods are:

| Method | Intended behavior |
|---|---|
| `debug()` | Emit a `DEBUG` record |
| `info()` | Emit an `INFO` record |
| `warning()` | Emit a `WARNING` record |
| `error()` | Emit an `ERROR` record |
| `critical()` | Emit a `CRITICAL` record |
| `exception()` | Emit an error record with exception information |
| `log()` | Emit a record with an explicitly supplied level |

The exact parameter signatures, return values, validation rules, and behavior when a method is called incorrectly remain to be defined.

### 6.3 Individual Configuration

An additional method has been requested for changing the configuration of an individual logger.

The proposed interface is:

```python
logger.configure(option, value)
```

Where:

- `option` identifies the configuration option.
- `value` supplies the new value.

The method must affect only the logger on which it is called.

The proposed behavior is:

- Validate the option.
- Validate the supplied value.
- Create or update the individual override.
- Leave other loggers unchanged.
- Preserve the global configuration.
- Support restoring inheritance from the global configuration.

The method name, accepted options, error types, and reset mechanism remain to be confirmed.

### 6.4 Logging Levels

A dedicated level type or enumeration is proposed to represent the five supported levels internally.

The final public representation of levels remains undecided.

---

## 7. Log Record Model

A common record model is required for the logging core, persistence, exception handling, custom fields, and the future viewer.

The following fields are proposed:

| Field | Description | Status |
|---|---|---|
| `timestamp` | Record date and time | Proposed; UTC recommended |
| `level` | Logging severity | Five levels accepted |
| `name` | Logger name | Proposed |
| `message` | Main record message | Proposed |
| `module` | Source module | Proposed |
| `file` | Source file | Proposed |
| `function` | Source function | Proposed |
| `line` | Source line number | Proposed |
| `exception` | Exception information and traceback | Proposed |
| Custom fields | Additional key-value data | Included in initial scope |

The record model has not yet been finalized.

Open questions include:

- Which fields are mandatory.
- Whether timestamps are always stored in UTC.
- How timestamps are formatted.
- How exceptions and tracebacks are represented.
- How source information is collected.
- How custom fields are stored and rendered.
- What happens when source information cannot be determined.

---

## 8. Configuration

### 8.1 Global Configuration

The `Manager` maintains global configuration.

The following options are currently proposed:

- Log-file name.
- Record output format.
- Inclusion of source information.
- Exception representation.
- General record representation settings.

The final list remains open.

### 8.2 Individual Configuration

A `Logger` may override selected global configuration options.

The log-file location will not be directly configurable by individual loggers in the initial version.

The complete list of permitted individual overrides remains to be decided.

### 8.3 Configuration Inheritance

The accepted inheritance model is:

- A logger inherits global values by default.
- An individual override takes precedence for that option.
- A global change updates inherited values.
- Removing an override restores the current global value.
- One logger's overrides do not affect another logger.

A dedicated mechanism for inspecting effective configuration and resetting individual overrides has not yet been finalized.

---

## 9. Record Output Format

LogMason is intended to support a configurable text template for formatting records.

An example supplied during planning is:

```text
[{name}] [{date}] [{time}]... {message}
```

This illustrates the desired style and is not necessarily the final default format.

### 9.1 Proposed Placeholders

The following placeholders are proposed:

| Placeholder | Meaning |
|---|---|
| `{name}` | Logger name |
| `{date}` | Record date |
| `{time}` | Record time |
| `{level}` | Logging level |
| `{message}` | Main message |
| `{exception}` | Exception information and traceback |
| `{module}` | Source module |
| `{function}` | Source function |
| `{line}` | Source line number |

The placeholder syntax and the default template remain to be confirmed.

### 9.2 Format Validation

The implementation should validate templates and report invalid placeholders clearly.

The following details remain open:

- Behavior when a placeholder is unknown.
- Behavior when an optional field is absent.
- How custom fields are referenced.
- How literal braces are escaped.
- Whether format validation occurs when configuration is changed or when a record is emitted.

### 9.3 Output and Storage Format

The intended storage format is readable text in `.log` files.

The file will contain the rendered record representation rather than JSON Lines.

Structured custom fields may be supported internally while still being rendered as text.

---

## 10. Persistence

### 10.1 File Location

On Windows, log files will be stored under `%APPDATA%`.

The `Manager` will determine the specific subdirectory and location within that application-data area.

The user will not directly configure the file path in version `0.1.0`.

The exact directory structure remains undecided.

For Linux and macOS, an equivalent storage-location policy must be defined if those platforms are included in the initial release.

### 10.2 File Extension and Naming

Log files will use the `.log` extension.

The log-file name will be configurable through the supported configuration mechanism.

A default name such as `logmason.log` is proposed but has not been formally accepted.

The precise interaction between the global file name and multiple `Manager` instances remains to be specified.

### 10.3 File Encoding

Log files will use UTF-8 encoding.

### 10.4 Preservation of Existing Records

Opening a log file must not truncate or overwrite records from earlier executions.

New records will be appended to existing content.

The implementation must define how it handles file creation, existing files, and initialization failures.

### 10.5 Concurrency

Thread-safe access to shared file resources is recommended for the initial implementation.

The initial design is expected to target a single application process.

Guaranteed concurrent writes from multiple processes to the same file are outside the initial scope.

### 10.6 Rotation and File Size

Automatic rotation is excluded from version `0.1.0`.

Consequently, log files may grow indefinitely.

A future release may introduce file rotation, size limits, retention policies, or other storage-management features.

### 10.7 Resource Lifecycle

The `Manager` should own the persistence resources.

The following lifecycle features are proposed:

- A `close()` method.
- Proper flushing and closing of resources.
- Support for use as a context manager.
- Defined behavior when logging is attempted after closure.

The exact initialization strategy, error handling, and behavior after closure remain to be confirmed.

---

## 11. Exception Handling

Exception handling is included in the intended scope of version `0.1.0`.

The proposed `exception()` method will:

- Emit an error-level record.
- Include the active exception information and traceback when available.
- Preserve exception information in the resulting log representation.

Normal error methods will remain usable without an associated exception.

The behavior of `exception()` when no exception is active must be explicitly defined.

LogMason will not automatically guarantee the removal of sensitive data from messages or tracebacks. Applications must avoid logging secrets and sensitive information.

---

## 12. Custom Fields

Custom key-value fields are included in the intended scope of version `0.1.0`.

An illustrative usage example is:

```python
logger.info(
    "Request received",
    request_id="req-123",
    component="api",
)
```

The final syntax has not been confirmed.

### 12.1 Proposed Value Types

JSON-compatible values are proposed for the initial implementation:

- Strings.
- Numbers.
- Booleans.
- Null values.
- Lists of supported values.
- Objects containing supported values.

The exact treatment of nested values and non-serializable Python objects remains open.

### 12.2 Reserved Fields

Internal field names should be reserved to prevent collisions.

Potential reserved names include:

- `timestamp`
- `level`
- `name`
- `message`
- `exception`
- `module`
- `file`
- `function`
- `line`

The final reserved-name policy must be confirmed.

### 12.3 Validation and Representation

Invalid custom field values should produce clear validation errors rather than being silently discarded.

The final specification must define:

- Accepted value types.
- Nested data support.
- Reserved-name handling.
- Custom field formatting.
- Behavior when formatting fails.
- Whether fields may be transformed or omitted before persistence.

---

## 13. Error Handling and Reliability

LogMason should make configuration and persistence failures explicit.

The proposed behavior includes:

- Detecting invalid configuration options.
- Detecting invalid configuration values.
- Detecting invalid output templates.
- Reporting file-opening failures.
- Reporting write failures.
- Defining behavior when the manager has been closed.
- Avoiding silent data loss where practical.

The exact exception types and recovery policies remain undecided.

The initial release will not promise unlimited storage, guaranteed operation under all resource failures, or lossless recording under every possible failure condition.

---

## 14. Distribution and Compatibility

The following technical choices are proposals rather than accepted decisions.

| Aspect | Current proposal |
|---|---|
| Minimum Python version | Python 3.11 |
| Operating systems | Windows, Linux, and macOS |
| Runtime dependencies | Python standard library only |
| Build backend | setuptools |
| Packaging configuration | `pyproject.toml` |
| Test framework | pytest |
| Code-quality tooling | Ruff |
| Package layout | `src/logmason/` |
| Installation | `pip` |
| Direct-copy usage | Copy the `logmason` package directory |

### 14.1 Platform Support

Windows storage is expected to use `%APPDATA%`.

A platform-appropriate storage location must be defined for Linux and macOS before cross-platform support can be considered complete.

Supported platforms must be tested rather than merely declared compatible.

### 14.2 Dependency Policy

Using only the Python standard library at runtime is proposed to minimize installation requirements and maintenance burden.

Development dependencies may include testing and code-quality tools.

The dependency policy remains to be confirmed.

### 14.3 PyPI Publication

The intended distribution name is `logmason`.

Its availability must be verified before publication. No registration or publication should take place until the project is ready for release.

---

## 15. Testing and Quality Assurance

Automated tests are required for the initial release.

The currently known test result is limited to a successful import test. It does not demonstrate that the logging core or persistence system is complete.

### 15.1 Proposed Test Coverage

The initial test suite should cover:

1. Package import.
2. Manager creation.
3. Logger creation.
4. Rejection of a logger without an explicit manager.
5. Independent loggers sharing the same name.
6. Capture of all five logging levels.
7. Global configuration inheritance.
8. Individual configuration overrides.
9. Restoration of inherited values.
10. Configuration validation.
11. Output template validation.
12. UTF-8 file persistence.
13. Preservation of existing records.
14. Exception and traceback recording.
15. Custom field handling.
16. Reserved field validation.
17. File initialization and write failures.
18. Resource closure.
19. Thread-safe writing, if included in the finalized requirements.
20. Package installation and direct-copy usage.

### 15.2 Acceptance Criteria

Version `0.1.0` should not be considered ready until:

- Its public API is documented.
- Its accepted behavior is implemented.
- Its automated tests pass.
- Its persistence and error-handling behavior are documented.
- Installation and import methods have been tested.
- Known limitations are documented.
- The release does not claim features that have not been implemented.

The precise acceptance criteria and required platform test matrix remain to be finalized.

---

## 16. Repository Structure

The following structure is a proposed starting point:

```text
LogMason/
├── src/
│   └── logmason/
│       └── __init__.py
├── tests/
│   └── test_import.py
├── PROJECT_SPEC.md
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
├── .gitignore
└── pyproject.toml
```

This structure is not a verified inventory of the current repository.

The project has previously been worked on using Visual Studio Code on Windows. The known local working directory is:

```text
D:\Projects\LogMason
```

The current status of every local and remote file must be checked before making repository-wide claims.

The README should remain concise and must not promise functionality that has not yet been implemented.

---

## 17. Documentation and Community Files

The project intends to maintain:

- `README.md` — Project introduction, installation, examples, and implemented capabilities.
- `PROJECT_SPEC.md` — Project specification, decisions, proposals, and open questions.
- `LICENSE` — MIT License.
- `CONTRIBUTING.md` — Contribution guidelines.
- `CODE_OF_CONDUCT.md` — Community conduct expectations.
- `SECURITY.md` — Security reporting guidance.

Documentation must distinguish between implemented functionality, planned functionality, and deferred functionality.

The initial README should avoid making promises about the future viewer or other features that are not yet available.

---

## 18. Deferred Features and Future Development

The following areas may be considered after the initial release:

- Separate terminal-based log viewer.
- Configurable automatic viewer startup.
- Interactive log filtering.
- Communication between the logging core and viewer process.
- More advanced storage management and log rotation.
- Multi-process file writing.
- External configuration files.
- Remote logging.
- Plugin support.
- Full interoperability with Python's standard `logging` module.
- Additional configuration and record-processing features.

Their inclusion, order, and release targets have not been decided.

Future development must preserve the independence of logger instances and the global/individual configuration model unless a deliberate architectural decision changes them.

---

## 19. Outstanding Decisions

The following questions remain open and should be resolved before the specification is considered complete.

### 19.1 Public API

- Final constructor signatures.
- Final logging method signatures.
- Final `configure()` name and behavior.
- Method for removing individual overrides.
- Public representation of logging levels.
- Return values and validation errors.

### 19.2 Configuration

- Complete list of global options.
- Complete list of options that may be overridden per logger.
- Effective configuration inspection.
- Default output format.
- Configuration validation rules.

### 19.3 Record Model and Formatting

- Required and optional record fields.
- Timestamp representation and time-zone policy.
- Final set of format placeholders.
- Custom field formatting.
- Handling of absent and unknown placeholders.
- Escaping of literal braces.
- Exception representation.

### 19.4 Persistence

- Exact directory layout under `%APPDATA%`.
- Default log-file name.
- File naming behavior with multiple managers.
- File initialization strategy.
- Write-error reporting and recovery.
- Thread-safety guarantees.
- Handling of simultaneous access.
- Platform-specific storage locations.

### 19.5 Exceptions and Custom Fields

- Behavior of `exception()` outside an active exception handler.
- Accepted custom field types.
- Nested object support.
- Reserved field names.
- Validation and conversion behavior.

### 19.6 Compatibility and Packaging

- Minimum Python version.
- Supported operating systems for version `0.1.0`.
- Runtime dependency policy.
- Build backend.
- Development tools.
- Package installation and direct-copy validation.

### 19.7 Quality and Release

- Detailed acceptance criteria.
- Required test coverage.
- Platform test matrix.
- Resource and failure behavior.
- Release readiness criteria.
- PyPI name verification.

---

## 20. Recommended Next Steps

The recommended development sequence is:

1. Finalize the persistence architecture and file naming.
2. Finalize the record format and template placeholders.
3. Define the complete global and individual configuration model.
4. Confirm the public API.
5. Define exception handling and custom field validation.
6. Confirm supported Python versions and operating systems.
7. Finalize error-handling behavior and acceptance criteria.
8. Implement the logging core.
9. Expand automated tests.
10. Update documentation to reflect implemented behavior.
11. Verify package installation and direct-copy usage.
12. Verify the intended PyPI distribution name before publication.

---

## 21. Current Project Status

LogMason has an established project direction and a preliminary scope for version `0.1.0`.

The following architectural principles are accepted:

- Independent logging system.
- Five logging levels.
- No minimum severity filtering in the core.
- Explicit manager requirement for each logger.
- Independent logger identity.
- Global configuration with individual overrides.
- MIT-licensed open-source development.
- A future terminal viewer, deferred from the initial release.

The initial persistence direction is also established at a high level: readable `.log` files, UTF-8 encoding, preservation of existing records, and manager-controlled storage under `%APPDATA%` on Windows.

However, several implementation details and recent technical proposals still require confirmation.

**This document records the current state of planning. It does not claim that the proposed functionality has already been implemented or tested.**
