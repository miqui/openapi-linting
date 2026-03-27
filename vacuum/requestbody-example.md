```yaml
extends:
  - spectral:oas
  - vacuum:recommended

rules:
  operation-requires-requestbody-schema-and-example:
    description: "All POST, PUT, and PATCH operations must define a requestBody with a schema and at least one example."
    message: "{{property}} must include requestBody.content with schema and example(s)."
    given: "$.paths[*][post,put,patch]"
    severity: error
    then:
      field: requestBody
      function: schema
      functionOptions:
        schema:
          type: object
          required: ["content"]
          properties:
            content:
              type: object
              additionalProperties:
                type: object
                required: ["schema"]
                properties:
                  schema:
                    type: object
                  example:
                    type: object
                  examples:
                    type: object
                    minProperties: 1

  operations-have-description:
    description: "Every API operation must include a clear description."
    message: "Operation must have a non-empty description."
    given: "$.paths[*][*]"
    then:
      field: description
      function: truthy
    severity: error

  servers-use-https:
    description: "All server URLs must use HTTPS."
    message: "Server URL must start with https://"
    given: "$.servers[*].url"
    then:
      function: pattern
      functionOptions:
        match: "^https://"
    severity: error

  responses-require-success-schema:
    description: "Successful responses (2xx) must define a schema."
    message: "2xx response must include a content schema."
    given: "$.paths[*][*].responses[2*0]"
    then:
      field: content
      function: schema
      functionOptions:
        schema:
          type: object
          required: ["application/json"]
    severity: warn

  operations-require-tags:
    description: "All operations must be tagged."
    message: "Operation must have at least one tag."
    given: "$.paths[*][*]"
    then:
      field: tags
      function: truthy
    severity: warn
```
