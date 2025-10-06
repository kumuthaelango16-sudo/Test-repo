import groovy.json.JsonSlurper
import groovy.json.JsonOutput

// Step 1: Read your normal JSON file
def input = new JsonSlurper().parse(new File('input.json'))

// Step 2: Convert all key/value pairs to DSL lines
def dslLines = input.collect { key, value ->
    "    ${key} = '${value}'"
}.join("\n")

// Step 3: Wrap it in a DSL block
def dslScript = "dsl-artifactVersion {\n${dslLines}\n}"

// Step 4: Prepare CBCD JSON format
def cbcdJson = JsonOutput.toJson([dsl: dslScript])

// Step 5: Save it to a file (optional)
new File('dslRequest.json').text = cbcdJson

println "✅ CBCD DSL JSON generated successfully!"
println cbcdJson
