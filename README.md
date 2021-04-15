# Local Veracode Scan action

This action will do local veracode scan for the specified package.  

1. Download `veracode` package
2. Unzip it
3. Run the jar file with provisioned parameters

## Inputs

parameter | required | default | note
----|---|---|---
id | true | | The veracode API ID
secret | true || The veracode API secret
severity | false | Very High, High |  The severity to fail this run
input-file | false | input.zip | The input package that meets veracode requirement
output-file | false | results.json | The result report file in JSON format, can be converted into SARIF file

## Example usage

We can use the following workflow to scan our package and report its security issues on github `security` tab.  

```yaml
# first use this action to generate JSON report
- uses: Rugal/local-veracode-scan-action@v1.0.0
  continue-on-error: false
  with:
    id: ${{secrets.VERACODE_API_ID}}
    secret: ${{secrets.VERACODE_API_SECRET}}
    input-file: target/binary.jar
# then upload the results.json file
- uses: actions/upload-artifact@v1
  with:
    name: ScanResults
    path: results.json
# then convert JSON format to SARIF
- name: Convert pipeline scan output to SARIF format
  id: convert
  uses: veracode/veracode-pipeline-scan-results-to-sarif@master
  with:
    pipeline-results-json: results.json
# finally upload sarif file so you can see result in Github security tab
- uses: github/codeql-action/upload-sarif@v1
  with:
    # Path to SARIF file relative to the root of the repository
    sarif_file: veracode-results.sarif
```
