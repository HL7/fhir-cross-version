### Frequently Asked Questions

Index:
* 2026.01.29: [What is in the Cross-Version package?](#faq20260129-01)
* 2026.02.03: [How do I handle required elements with required bindings?](#faq20260203-01)
* 2026.02.06: [How do I handle resources that do not exist in my FHIR version?](#faq20260206-02)
* 2026.02.06: [How do I handle type-mapping across FHIR versions?](#faq20260206-01)
* 2026.02.09: [How do I handle required reference elements?](#faq20260209-01)
* 2026.02.09: [What are the CodeSystems used in the element concept maps?](#faq20260209-02)

#### What is in the Cross-Version package?
<a name="faq20260129-01"></a>



#### How do I handle resources that do not exist in my FHIR version?
<a name="faq20260206-02"></a>

There are a few types of cases where the resource someone is looking at in one version of FHIR does not exist in another version:
1. the resource was added or removed
2. the resource was renamed, split into multiple resources, or merged into another resource

In order to track the relationships of resource between versions, a set of `ConceptMaps` are used. The content below is from the current version at the time of writing, but is not authoritative. The ultimate source-of-truth are the maps that are maintained at: in the [HL7 FHIR-Cross-Version GitHub Repository](https://github.com/HL7/fhir-cross-version/), specifically in the [`input/resources` directory](https://github.com/HL7/fhir-cross-version/tree/main/input/resources).

In the first case, a resource definition exists in one version of FHIR and there is *no equivalent* resource in another. For example, the [R5 `ActorDefinition` resource](https://hl7.org/fhir/R5/actordefinition.html) does not exist in R4, and has no analog. In this case, the contents can be represented by using a `Basic` resource in the target version (e.g., FHIR R4) that contains an extension with the actual contents and a `Basic.code` indicating the source content representation. For convenience, these packages define profiles on `Basic` that specify which extension to use, the required `code` value, etc.. Note that some elements are mapped into equivalent elements on `Basic` instead of being contained in the extensions.  E.g., `ActorDefinition.meta` is not defined, because `Basic.meta` must be used.

In the second case, the proper target(s) can be found using the lookup pages defined in these packages. Extensions are defined with contexts appropriate to use.  Note that when a resource is split, not every source element is available in each target. For example, if resource `A` is split into `B` and `C`, elements that are mapped into `B` directly are not available as extensions in `C`, since they *should* be conceptually incompatible.


#### How do I handle type-mapping across FHIR versions?
<a name="faq20260206-01"></a>

FHIR versions both add and remove types from the lists of definitions and from the set that are allowed in the `Extension.value[x]` element. The process for representing this data varies depending on whether the source is a primitive type (e.g., `canonical`, `integer64`) or a complex type (e.g., `Attachment`, `CodeableReference`).

In order to manage the type mappings, a set of `ConceptMaps` are used. The content below is from the current version at the time of writing, but is not authoritative. The ultimate source-of-truth are the maps that are maintained at: in the [HL7 FHIR-Cross-Version GitHub Repository](https://github.com/HL7/fhir-cross-version/), specifically in the [`input/types` directory](https://github.com/HL7/fhir-cross-version/tree/main/input/types).

##### Primitive Types

Primitive types are *necessarily* mapped into other primitive types that can contain their values. This is necessary because there needs to be a single element that can contain a value equivalent to the source value.

The types themselves are mapped in up-to two stages. The first stage is the 'preferred' source -> target mapping, and is based on the specific versions in question (because it takes into account the specific types available). One set of primitive types are the most straightforward, as they have existed in all releases of FHIR and thus require no alternative representation:

* `base64Binary`
* `boolean`
* `code`
* `date`
* `dateTime`
* `decimal`
* `id`
* `instant`
* `integer`
* `markdown`
* `oid`
* `positiveInt`
* `string`
* `unsignedInt`
* `uri`

The remaining (current) primitive types are represented based on a primitive type that can contain the required data:

* `canonical`, added in R4, maps to `uri` in earlier versions
* `integer64`, added in R5, maps to `string` in earlier versions
* `time`, added in STU3, maps to `string` in earlier versions
* `url`, added in R4, maps to `uri` in earlier versions
* `uuid`, added in STU3, maps to `uri` in earlier versions

In total, that provides a *base* mapping table that looks like:

| Source Type | Preferred Target | Target when not available |
| ----------- | ---------------- | --------------- |
| `base64Binary` | `base64Binary` | n/a (always available) |
| `boolean` | `boolean` | n/a (always available) |
| `canonical` | `canonical` | `uri` |
| `code` | `code` | n/a (always available) |
| `date` | `date` | n/a (always available) |
| `dateTime` | `dateTime` | n/a (always available) |
| `decimal` | `decimal` | n/a (always available) |
| `id` | `id` | n/a (always available) |
| `instant` | `instant` | n/a (always available) |
| `integer` | `integer` | n/a (always available) |
| `integer64` | `integer64` | `string` |
| `markdown` | `markdown` | n/a (always available) |
| `oid` | `oid` | n/a (always available) |
| `positiveInt` | `positiveInt` | n/a (always available) |
| `string` | `string` | n/a (always available) |
| `time` | `time` | `string` |
| `unsignedInt` | `unsignedInt` | n/a (always available) |
| `uri` | `uri` | n/a (always available) |
| `url` | `url` | `uri` |
| `uuid` | `uuid` | `uri` |

The second part of type-mapping primitives are a set of mappings that indicate what is *allowed* to map, based on element context. For example, an element might move between `string`, `code`, and `id` over the course of development. Understanding how primitives related to each-other is purely based on their "value domain" (the set of values that can be represented by the type). Primitive types do not hold the context required to evaluate a "concept domain" (what the contained value *means*) - that context must be provided by the element that uses a type. The source-of-truth for the primitive mappings are defined in the [`input/types/ConceptMap-types-fallback.json`](https://github.com/HL7/fhir-cross-version/tree/main/input/types/ConceptMap-types-fallback.json) ConceptMap, which applies across all versions.

For example, the `string` type entry from that file looks like:
```json
{ "code" : "string", "target" : [
    { "code" : "string", "relationship" : "equivalent" },
    { "code" : "canonical", "relationship" : "source-is-broader-than-target" },
    { "code" : "code", "relationship" : "equivalent" },
    { "code" : "id", "relationship" : "source-is-broader-than-target" },
    { "code" : "integer64", "relationship" : "source-is-broader-than-target" },
    { "code" : "markdown", "relationship" : "equivalent" },
    { "code" : "oid", "relationship" : "source-is-broader-than-target" },
    { "code" : "uri", "relationship" : "source-is-broader-than-target" },
    { "code" : "url", "relationship" : "source-is-broader-than-target" },
    { "code" : "uuid", "relationship" : "source-is-broader-than-target" }]},
```

This indicates that an element defined as `string` in a version of FHIR *is allowed* to map to an element defined as any of: `string`, `canonical`, `code`, `id`, `integer64`, `markdown`, `oid`, `uri`, `url`, or `uuid`. The relationship describes the difference in values that can be contained by the related types.

##### Complex Types

Mapping of complex types fall into three general scenarios:
1. mapping an element that has the same type in both versions
2. mapping an element that does not exist, but the type does exist and is a valid `Extension.value[x]` type
3. everything else

The first case, mapping an element that has the same type in both versions, is the simplest. In this scenario, any elements that need extensions for the type are applied as extensions within the type itself.  For example, consider representing the [`R5 AllergyIntolerance.note`](https://hl7.org/fhir/R5/allergyintolerance-definitions.html#AllergyIntolerance.note) element in the [`R4 AllergyIntolerance.note`](https://hl7.org/fhir/R4/allergyintolerance-definitions.html#AllergyIntolerance.note) element. The element exists in both versions and has the same `Annotation` data type.  However, the [R5 `Annotation.author[x]`](https://hl7.org/fhir/R5/datatypes-definitions.html#Annotation.author_x_) element allows for a reference to `PractitionerRole` resources, whereas the [R4 `Annotation.author[x]`](https://hl7.org/fhir/R4/datatypes-definitions.html#Annotation.author_x_) element does not. In this case, there will be an extension available for `R4 Annotation.author[x]` that allows the additional reference.

The second case, mapping an element that does not exist but has a type that can map into an `Extension.value[x]` type, is still fairly straightforward. In this scenario, an extension is defined that uses the target-version datatype, noting that elements may still contain further extensions. For example, consider representing [R5 `Appointment`](https://hl7.org/fhir/R5/appointment.html) content via an [R4 `Appointment`](https://hl7.org/fhir/R4/appointment.html). Specifically, FHIR R5 added the element [`Appointment.note`](https://hl7.org/fhir/R5/appointment-definitions.html#Appointment.note), which is of type `Annotation`. There will be an extension defined for the R4 `Appointment` resource that *uses* the R4 `Annotation` type. If the R5 content also uses an `author` represented by a `PractitionerRole`, the extension defined in the above scenario (1) will be used there.

In the third case, anything not handled by the other scenarios, there is a complex extension defined that represents the type. For example, when trying to represent data from an [R5 `CodeableReference`](https://hl7.org/fhir/R5/references.html#CodeableReference) in FHIR R4 - where the type does not exist. In this case, a complex extension is defined that represents the entire contents of the complex type (each element becomes a slice in the extension). The extension *also* defines a slice using the standard [`_datatype` extension](https://hl7.org/fhir/extensions/StructureDefinition-datatype.html) to indicate that the content was *originally* a `CodeableReference`.


Note that the relationships between types across versions are also managed as version-to-version ConceptMap resources in the files at [`input/types`](https://github.com/HL7/fhir-cross-version/tree/main/input/types). This tracks (for example) when a type is renamed, so that it can be usefully used across versions.


#### How do I handle required elements with required bindings?
<a name="faq20260203-01"></a>

When an element is **required** (minimum cardinality of 1) and has a **required** terminology binding, it **must** be present in the instance and its value **must** come from the element's bound value set. Extensions can add additional information, but they **do not relax** the base rule: the base element still needs a valid value.

When you need to represent a concept that cannot be expressed using the required binding's value set (for example, when carrying semantics from a different FHIR release), follow this approach:

1.  **Populate the required element with the best available base code.**  
    Choose the code from the bound value set that most accurately describes the data you have. If no perfect match exists, choose the closest truthful approximation.

2.  **Use an extension to represent values that the base code cannot express.**  
    If an extension exists specifically to represent the additional concept (e.g., a cross-version extension), populate it according to the extension definition, including any required binding on the extension. Cross-version packages commonly provide both the extension definitions and the associated value sets for the extension content.

3.  **Avoid duplicating the same meaning in both places unless needed for semantic clarity.**  
    If the base element's value fully conveys the intended meaning, the extension is typically unnecessary. If two versions use the "same" code token but with different semantics, an extension may be appropriate to carry the version-specific intent.

##### Example: `MedicationRequest.status`
In FHIR R4, `MedicationRequest.status` is required and has a required binding. Therefore an R4 `MedicationRequest` instance must include `status` with a code allowed by the R4 binding. If you need to convey an R5-specific status not available (or not equivalent) in R4, you still populate the R4 `status` with the best available R4 code, and then add the relevant cross-version extension to express the more specific R5 meaning. The extension value must conform to the extension's own binding rules.

If you are describing a generic use (e.g., authoring an implementation guide), it is recommended to _specifically_ list which codes are appropriate when doing this mapping.

##### Summary

* Is the element required? If yes, **always populate it**.
* Is the binding required? If yes, **choose a code from the bound value set**.
* Need a concept outside the value set?
    * Pick the **closest truthful base code**, then
    * Add an extension designed for the missing semantics, and **validate against the extension's value set**.
* Document mapping choices (especially cross-version mappings) in your IG.


#### How do I handle required reference elements?
<a name="#faq20260209-01"></a>

When an element is **required** (minimum cardinality of 1) and is a `reference` type, it **must** be present in the instance. This can be problematic when the desired reference *target* is a type that is not available in the version of FHIR being used. Extensions can add additional information, but they **do not relax** the base rule: the base element still needs a valid value.

When you need to represent a reference that cannot be expressed using the required element's allowed targets (for example, when using a type added in a later FHIR release), follow this approach:

1.  **Populate the required element with the best available information.**  
    The `reference` datatype includes elements such as `display` and `identifier`, which can be useful without being resolvable.

2.  **Use an extension to represent values that the base code cannot express.**  
    If an extension exists specifically to represent the additional reference (e.g., a cross-version extension), populate it according to the extension definition, including any target constraints on the extension. Cross-version packages provide the extension definitions with allowed targets, both in terms of the target resource type and the cross-version profile.

3.  **Avoid duplicating the same meaning in both places unless needed for semantic clarity.**  
    If the base element's value fully conveys the intended meaning, the extension is typically unnecessary. If the reference can be represented by something native to the FHIR version that is being used, that is the preferred mechanism to use.

##### Example: `DiagnosticReport.media.link`
In FHIR R4, `DiagnosticReport.media.link` is required and has a constrained reference to only allow references to `Media`. Therefore an R4 `DiagnosticReport` instance that wants to include the `DiagnosticReport.media` element must include a valid `link` element. In FHIR R5, the `Media` resource type was merged into the `DocumentReference` resource, which means that the equivalent R5 element (`DiagnosticReport.media.link`) has only a valid reference target of `DocumentReference`.

In this case, the R4 `DiagnosticReport.media.link` would have something useful (e.g., a `display` element describing the content and possibly detailing *why* it is a `DocumentReference` instead of a `Media`) and use the appropriate extension to add the actual reference.

##### Summary

* Is the element required? If yes, **always populate it**.
* Is the reference target possible to represent via an expected target? If yes, use the expected representation.
* Need a reference target outside the allowed targets?
    * Complete the base reference with **reference values as possible, even if unresolvable**, then
    * Add an extension designed for the missing target.
* Document expectations around use (especially different targets) in your IG.


#### What are the CodeSystems used in the element concept maps?
<a name="#faq20260209-02"></a>

In the ConceptMaps that reference elements, the maps make use of "Implicit Code Systems" as defined by the FHIR specification. Note that the definitions of implicit Code Systems for StructureDefinition resources has been added and removed from the FHIR core specification across versions.

The definition that the cross-version guides use is based on the *current* guidance, which (at the time of writing) is reproduced below.

> Some other parts of the FHIR infrastructure define set of concepts that may also be treated as code systems. This is most useful when mapping between systems using Concept Maps, but might also be useful for other code system related functionality (e.g. subsetting using Value Sets). The table below summarizes how to treat these items as a code system:

> | CodeSystem Element Value | Meaning |
> | ------------------------ | ------- |
> | URL (`CodeSystem.url`) | `StructureDefinition.url` |
> | Concept (`CodeSystem.concept`) | Each `StructureDefinition.snapshot.element` element corresponds to a code system concept. |
> | Code (`CodeSystem.concept.code`) | `StructureDefinition.snapshot.element.id` |
> | Display (`CodeSystem.concept.display`) | `StructureDefinition.snapshot.element.label` if one exists, otherwise `StructureDefinition.snapshot.element.short` |
> | Definition (`CodeSystem.concept.definition`) | `StructureDefinition.snapshot.element.definition` |

