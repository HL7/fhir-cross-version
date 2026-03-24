### 0.0.1-snapshot-3

* Fix: resources mapping to `Basic` still need a `[Source.]code` extension, `Basic.code` has a necessary meaning. Excluded `Basic.code` from automatic removal when mapping to `Basic`.
* Fix: non-resource structures should not create profiles of `Basic` resources. Only resource structures should create `Basic` profiles.
* Updated to use language-based fragment inclusions.
* Added default language of `en-US` so translations can be supported.
* Added `https://www.iana.org/time-zones` to exclusion set (never need to generate)
* Removed dependency on SUSHI for package generation
* Constrained `Extension.value[x]` to `0..0` when creating complex extensions that do not contain direct value slices.
* Added `ConceptMap` resources for `ValueSet` resources that map between the source and target version.
* Added `ConceptMap` resources for resource and element mapping between source and target versions.
* Added Lookup files for Value Sets
* Added profile links to the lookup files for Resource extensions that target `Basic`
* Added profiles for all resource mappings (expanded from only ones that targeted `Basic`)
* Extensions that include multiple types and require an extension (e.g., a datatype that does not exist in the target) have been moved to `value[TypeName]` slices to avoid conflicts on multiple elements with the same name.
* Extensions that were targeting elements that were choice types have their targets moved up to a parent - extensions cannot exist directly on the choice type, and having so many slices that repeat content was untenable.
* Fix: `Basic`->`Basic` resource profile is a 'standard' resource profile, not a Cross-Version resource representation.
* Fix: any element that gets a definition requires all children underneath it to be part of the definition.
* Added `http://hl7.org/fhir/StructureDefinition/version-specific-use` to all exported extensions and profiles.
* Updated mappings to account for 'mapped to other location'.  E.g., a value set may be fully mapped if all concepts are mapped into one of two different target value sets.
* Fix: extension id "short" abbreviations were too short and creating collisions (e.g., both `Specimen.collection.device` and `Specimen.container.device` shortened to `Specimen.co.device`).
* Note: R5 CodeSystem `http://terminology.hl7.org/CodeSystem/operation-outcome|2.0.0` (from THO) is updated during export to have id `operation-outcome-tho` and name `OperationOutcomeTHO` due to collisions with the R5 CodeSystem `http://hl7.org/fhir/operation-outcome`.
* Fix: removed CodeSystems that are experimental and have 'example' in their id from exports.
* Fix: added prefixes to all exported content to avoid collisions
* Updated structure lookup files to link to the ancestor extension definition when the target is a slice in an extension definition.
* Fix: all extension slicing is `open` in extension definitions
* Added: use `alternate-canonical` to represent any canonical definition
* Added: use `alternate-reference` to represent any reference definition
* Added: use `alternate-canonical` and `alternate-reference` in place of specific parts of choice types.
* Added link to Confluence FAQs page
* Added replacement URLs for extensions in the extensions pack derived from CRMI
* Added replacement URLs for extensions that should be substituted from the Subscription Backport IG

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