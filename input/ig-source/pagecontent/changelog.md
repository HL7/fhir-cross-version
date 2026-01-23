### 0.0.1-snapshot-3

* Fix: resources mapping to `Basic` still need a `[Source.]code` extension, `Basic.code` has a necessary meaning. Excluded `Basic.code` from automatic removal when mapping to `Basic`.
* Fix: non-resource structures should not create profiles of `Basic` resources. Only resource structures should create `Basic` profiles.
* Updated to use language-based fragment inclusions.
* Added default language of `US-en` so translations can be supported.
* Added `https://www.iana.org/time-zones` to exclusion set (never need to generate)
* [ ] Remove dependency on SUSHI
* [ ] Constrain `Extension.value[x]` to `0..0` when creating complex extensions
* [ ] https://hl7.org/fhir/uv/xver-r5.r4/0.0.1-snapshot-2/StructureDefinition-ext-R5-ValueSet.ex.co.property.html is still closed - why?
    * Also has a sub-property `value[x]`, which should be `value`
* [ ] Add ConceptMap resources for element / outcome navigation
* [ ] Update Lookup files to include the 'parent' extension when result is to use a parent extension
* [ ] Add Lookup files for Value Sets
* [ ] Add profile links to the lookup files for Resource extensions that target `Basic`
* [ ] Port Search Parameters for new resources
    * [ ] Determine if we can add new search parameters due to additional elements
* [ ] Port Operation Definitions for new resources
* [ ] Add ImplementationGuide.definition.grouping to organize resources in the IG
* [ ] Add support for STU3 package generation
* [ ] Add support for DSTU2 package generation

### 0.0.1-snapshot-2

* Fix issue with modifier extensions:
    * Modifier element -> Modifier element : extension
    * Modifier element -> Backbone element (not modifier) : modifier extension
    * Modifier element -> Primitive-type element (not modifier) : modifier extension, context moves up a level
    * Modifier element -> Primitive-type element (array, not modifier) : currently unresolvable, but also has not happened yet
* Fix: canonical targets that do not exist (use `alternate-canonical` extension)
* Fix: reference targets that do not exist (use `Basic` as target)
* Define profiles for `Basic` resources to represent undefined resource types
* Define profiles for existing resources to represent other versions of the same resource type
* Define cross-version value sets for all expandable and not-excluded value sets instead of only ones with `required` bindings
* Fix: `.url` appearing twice in snapshots
* Fix: some elements being out-of-order in snapshots
* Fix: `_datatype` being out-of-order in some extensions (see R5:`SubscriptionStatus.eventsSinceSubscriptionStart`)
* Fix: slicing discriminator being `closed` instead of `open` in some cases
* Fix: `StructureDefinition.name` must begin with a capital letter (`sdf-0` / `cnl-0`)
* Fix: `ValueSet.name` must begin with a capital letter (`sdf-0` / `cnl-0`)
* Fix: cross-version profiles for `Basic` resources had incorrect root extension slice names
* Fix: cross-version profiles for `Basic` resource were missing `sliceName` on the `extension` element
* Fix: cross-version profiles for `Basic` should use *pattern* instead of *fixed* for the `code` element (avoid over-constraining the value)
* Added `changelog.md` file to generated packages (see XVerProcessorDbPackage.cs)
* Added `lookup.md` file to generated single-version packages as index file for all resource lookups
* Fix: sushi-config.yaml `name` property was not properly formatted (`ig-0`)
* Added `ignoreWarnings.txt` file to generated packages to suppress known non-critical warnings during IG publishing
* Fix: ValueSets that have `equivalent` definitions in target versions were referencing the source version number
* Added porting of source `CodeSystem` resources from core packages
* Fix: replacing of 'special' FHIR links formatted as `[[[{structure[#fragment]}]]]` by converting to version-specific markdown links
* Added generation of unexpandable value sets by copying the source `ValueSet.compose` element to an XVer ValueSet
* Added `useContext` from source ValueSets to equivalent XVer ValueSets
* Added `jurisdiction` from source ValueSets to equivalent XVer ValueSets
* Fix: DSTU2 `usageContext` elements were not loading correctly
* Fix: DSTU2 `usageContext` element processing was not categorizing jurisdiction values correctly (now moving those values to `jurisdiction` elements)
* Fix: XVer ValueSets and CodeSystems were being exported with an empty `structuredefinition-wg` extension
* Fix: Checking `definition` and `comment` elements for HTML Anchor links and converting them to markdown links
* Added External Inclusions to support `http://terminology.hl7.org/CodeSystem/designation-usage`
* Added `ValueSet.compose` to generated XVer ValueSets.
* Fix: Generated value sets will no longer allow for `id` values longer than 64 characters.
* Added `publisher` and `contact` elements for definitional resources if none are present on canonical resources
* Fix: hl7.terminology.r4b does not appear to be published on the same regularity as other versions - changed to the r4 package.
* Added `hl7.terminology@5.0.1` to source material for R5
* Added automatic override of `STU3:ElementDefinition.binding.valueSet[x]` and `R4:ElementDefinition.binding.valueSet` as `equivalent` despite the type changes.
* Added mapping in `fhir-cross-version` repo for `DSTU2:Practitioner` to also map to `STU3:PractitionerRole`
* Added mapping in `fhir-cross-version` repo for `R4B:Media` to map to `R5:DocumentReference`
* Updated mapping in `fhir-cross-version` repo for `R4B:Questionnaire.item.type` to be *related to* `R5:Questionnaire.item.answerConstraint` (instead of *equivalent*).
* Updated Lookup files to include links to source elements, target elements, basic elements, cross-version extensions, and substitution extensions.
* Fix: some exported ValueSets had duplicate code definitions based on multiple subsumtion paths.
* Fix: additional suppression messages.
* Fix: updated `vocab` WG publisher to `HL7 International / Terminology Infrastructure`

### 0.0.1-snapshot-1

* Initial published version.