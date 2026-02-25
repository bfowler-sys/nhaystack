# NHaystack Maintainer Build and Signing Guide

This repository has been migrated to build with a modern Niagara plugin/toolchain
while remaining compatible with the existing module source layout.

## Prerequisites

- Niagara installed and licensed on this machine.
- Environment variables:
  - `NIAGARA_HOME` (example: `C:\Niagara\EC-Net Facilities-4.15.2.38`)
  - `NIAGARA_USER_HOME` (example: `C:\Users\<you>\Niagara4.15`)
- Local signing profile at:
  - `%USERPROFILE%\.tridium\security\niagara.signing.xml`

## One-time setup

Use Niagara's bundled Java runtime for repeatable local builds:

```powershell
$env:JAVA_HOME="$env:NIAGARA_HOME\jre"
$env:Path="$env:JAVA_HOME\bin;$env:Path"
```

## Build signed module jars

```powershell
.\gradlew.bat jar
```

Artifacts are produced in:

- `nhaystack-rt/build/signed-modules/nhaystack-rt.jar`
- `nhaystack-wb/build/signed-modules/nhaystack-wb.jar`

Unsigned/versioned jars are also in `build/libs`.

## Check signing certificate

```powershell
$profilePath="$env:USERPROFILE\.tridium\security\niagara.signing.xml"
.\gradlew.bat printCertificateInfo --profile-path="$profilePath" --alias=Niagara4Modules
```

## Create/refresh signing profile (if needed)

```powershell
$profilePath="$env:USERPROFILE\.tridium\security\niagara.signing.xml"
.\gradlew.bat createProfile --profile-path="$profilePath" --create
.\gradlew.bat generateCertificate --profile-path="$profilePath" --create --alias=Niagara4Modules
```

## Notes

- The build auto-detects Niagara plugin version from
  `%NIAGARA_HOME%\etc\m2\repository\com\tridium\tools\niagara-plugins`.
- If you need to force a specific plugin version:
  - set `NIAGARA_PLUGINS_VERSION` before running Gradle.
- You may still see Niagara plugin deprecation warnings (`niagaraModule {}` / `moduleParts {}`).
  They are non-fatal on Gradle 7.6.x and can be addressed in a later cleanup pass.
