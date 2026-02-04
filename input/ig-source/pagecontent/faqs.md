### Frequently Asked Questions

#### How do I handle required elements with required bindings?

When an element is **required** (minimum cardinality of 1) and has a **required** terminology binding, it **must** be present in the instance and its value **must** come from the element’s bound value set. Extensions can add additional information, but they **do not relax** the base rule: the base element still needs a valid value.

When you need to represent a concept that cannot be expressed using the required binding’s value set (for example, when carrying semantics from a different FHIR release), follow this approach:

1.  **Populate the required element with the best available base code.**  
    Choose the code from the bound value set that most accurately describes the data you have. If no perfect match exists, choose the closest truthful approximation.

2.  **Use an extension to represent values that the base code cannot express.**  
    If an extension exists specifically to represent the additional concept (e.g., a cross-version extension), populate it according to the extension definition, including any required binding on the extension. Cross-version packages commonly provide both the extension definitions and the associated value sets for the extension content.

3.  **Avoid duplicating the same meaning in both places unless needed for semantic clarity.**  
    If the base element's value fully conveys the intended meaning, the extension is typically unnecessary. If two versions use the "same" code token but with different semantics, an extension may be appropriate to carry the version-specific intent.

##### Example: `MedicationRequest.status`
In FHIR R4, `MedicationRequest.status` is required and has a required binding. Therefore an R4 `MedicationRequest` instance must include `status` with a code allowed by the R4 binding. If you need to convey an R5-specific status not available (or not equivalent) in R4, you still populate the R4 `status` with the best available R4 code, and then add the relevant cross-version extension to express the more specific R5 meaning. The extension value must conform to the extension’s own binding rules.

If you are describing a generic use (e.g., authoring an implementation guide), it is recommended to _specifically_ list which codes are appropriate when doing this mapping.

##### Summary

* Is the element required? If yes, **always populate it**.
* Is the binding required? If yes, **choose a code from the bound value set**.
* Need a concept outside the value set?
    * Pick the **closest truthful base code**, then
    * Add an extension designed for the missing semantics, and **validate against the extension’s value set**.
* Document mapping choices (especially cross-version mappings) in your IG.

