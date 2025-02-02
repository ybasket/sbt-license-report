# sbt-license-report

This plugin will allow you to report the licenses used in your projects. It requires 1.0.0+.

## Installation

Create a file in your project called `project/license.sbt` with the following contents:

    addSbtPlugin("com.github.sbt" % "sbt-license-report" % "<latest-version>")

## Usage

     > dumpLicenseReport

This dumps a report of all the licenses used in a project, with an attempt to organize them.  These are dumped, by default, to the `target/license-reports` directory.

If you happen to be using a multi project sbt build, you can instead use `dumpLicenseReportAggregate` (which collects the results
from `aggregate` on the root project) or `dumpLicenseReportAnyProject` (which collects the results for all projects in the sbt build).
In either case the results will be merged into a single report file in a format that mirrors `dumpLicenseReport`.

### Check licenses

If you want to check that only certain licenses are used in a project, you can use

     > licenseCheck

This ensures all licenses fall into one of the categories given by `licenseCheckAllow` which defaults
to a set of commonly allowed [OSS licenses](./src/main/scala/sbtlicensereport/SbtLicenseReport.scala#L173).

## Configuration

The license report plugin can be configured to dump any number of reports, but the default report
can be controlled via the following keys:

    import sbtlicensereport.license.{LicenseInfo, DepModuleInfo}

    // Used to name the report file, and in the HTML/Markdown as the
    // title.
    licenseReportTitle := "Example Report"

    // Add style rules to the report.
    licenseReportStyleRules := Some("table, th, td {border: 1px solid black;}")

    // The ivy configurations we'd like to grab licenses for.
    licenseConfigurations := Set("compile", "provided")

    // The order in which we find/choose licenses.  You can add your own license
    // detection here
    licenseSelection := Seq(LicenseCategory.BSD, LicenseCategory.Apache)

    // Attach notes to modules
    licenseReportNotes := {
      case DepModuleInfo(group, id, version) if group == "example" => "Made up artifact"
    }

    // Override the license information from ivy, if it's non-existent or
    // wrong
    licenseOverrides := {
      case DepModuleInfo("com.jsuereth", _, _) =>
        LicenseInfo(LicenseCategory.BSD, "BSD-3-Clause", "http://opensource.org/licenses/BSD-3-Clause")
    }

    // Want to exclude any artifacts from org.scala-lang organization. Note that transitive dependencies
    // of a filtered DepModuleInfo will NOT get filtered out
    licenseDepExclusions := {
        case DepModuleInfo("org.scala-lang", _, _) => true
    }

# Releasing

This plugin uses [sbt-ci-release](https://github.com/sbt/sbt-ci-release). To
release a new version you just need to tag and push the new tag. That will
trigger CI to publish a new release.

# License

This software is under the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0.html).
