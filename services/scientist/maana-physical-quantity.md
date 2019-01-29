# Physical Quantity

## Physical Quantity

This service is used for extracting physical quantities \( a.k.a. measured quantities\) from text, and performing dimensionally safe arithmetic, unit conversions and comparisons with them. This service uses a proprietery symbolic computing engine built on top of Stanford CoreNLP's named entity recognition library \([https://nlp.stanford.edu/software/](https://nlp.stanford.edu/software/)\).

This sequence of queries extracts pressure measurements from the sample sentence, converts them into head \(in feet of sea water\) and checks to see if the result is greater than an a provided alarm threshold.:

```text
query IS_OVERPRESSURE(
  $SOURCE:String!,
  $DENSITY_OF_SEAWATER:PhysicalQuantityInput!,
  $GRAVITATIONAL_ACCELERATION:PhysicalQuantityInput!,
  $ALARM_THRESHOLD:PhysicalQuantityInput!){

  extract(source:$SOURCE){
    entity{
      name
      div(divisor:$DENSITY_OF_SEAWATER){
        div(divisor:$GRAVITATIONAL_ACCELERATION)  {
          isGTE(rhs:$ALARM_THRESHOLD,precision:3)
        }}}}}
```

Queries of this kind facilitate equational reasoning with real world measurements, and can be used to augment the engineering knowledge models. Specifically, this service facilitates equational and graph based reasoning about physical quantities, units of measure, physical dimensions and systems of units.

## Assumptions

1. Entity mentions follow standard notational conventions for products, quotients and exponentials of units of measure
2. Although aliases are allowed, no unit of measure that is not a product, power or quotient can consist of more than one token.  For example: "1 Astronomical unit" would be invalid, but "1 AU" would be acceptable

## Warnings

1. The service uses a constructive pattern matching algorithm to parse physical quantities from text.    Acronyms, abreviations and part numbers can easily be misidentified as units of measure.    Some data cleansing may be required to get optimal performance.

## Schema
```ruby
type Info {
  # A unique identifier for the service
  id: ID!
  # A short, human readable name for the service
  name: String!
  # A human readable description for the service
  description: String
  # service readiness level: what functionality you can expect from this current version. Link to further info: https://github.com/maana-io/ServiceReadinessLevels
  srl:Int
}

# You must provide at least one of the arguments: name, formula or alias
input PhysicalDimensionRef {
  # A md5 has that uniquely identifies the physical dimension
  id: ID
  # A human readable name for the physical dimension (e.g. "LENGTH")
  name: String
  # The formula of the physical dimension
  formula: String
  # An alternate name for the physical dimension.
  alias: String
}

# You must provide at least one of the arguments: id or name
input PhysicalQuantityRef {
  # A md5 hash that uniquely identifies the physical quantity
  id: ID
  # (DEPRECATED. use name) A human readable name for the physical dimension (e.g. "LENGTH")
  parseFrom: String
  # A human readable name for the physical dimension (e.g. "LENGTH")
  name: String
}

# A System of units constraint.  You must provide an id or name.
input SystemOfUnitsRef {
  # A md5 hash that uniquely identifies the system of units
  id: ID
  # A short human readable name for the system of units (e.g. "mks")
  name: String
}

# A unit of measure reference.  You must provide an alias, id or name.
input UnitOfMeasureRef {
  # A md5 hash that uniquely identifies the unit of measure
  id: ID
  # The normal form of the unit of measure
  name: String
  # A surface form for the unit of measure
  alias: String
}

# A unit of measure prefix reference.  You must provide an alias, id or name.
input UnitOfMeasurePrefixRef {
  # A md5 hash that uniquely identifies the unit of measure
  id: ID
  # The normal form of the unit of measure
  name: String
  # A surface form for the unit of measure
  alias: String
}

# A physical dimension
input PhysicalDimensionInput {
  # A human readable name for the physical dimension (e.g. "LENGTH")
  name: String
  # The formula of the physical dimension
  formula: String!
  # A list of alternate names for the physical dimension.
  aliases: [String]
}

# UnitConversion input.
input UnitConversionInput {
  # The scaling factor used by the unit of measure conversion.
  multiplier: Float!
  # The offset used by the unit of measure conversion.
  offset: Float!
}

# A primary (elementary/atomic) unit of measure is not a product of prefixes and/or unit of measure factors.
input SimpleUnitOfMeasureInput {
  # The standard symbol used for the unit of measure (e.g. "m" for "meter")
  name: String!
  # The physical dimension of observations that this unit can quantify.
  dimension: PhysicalDimensionInput!
  # A list of possible alternative symbols used to denote this unit of measure (e.g. "meter", "metre")
  aliases: [String]
  # Get the unit conversion from this unit of measure to the standard SI unit for this dimension.
  siUnitConversion: UnitConversionInput!
}

input UnitOfMeasureFactorInput {
  # prefix of the unit of measure, if it has one.
  prefix: UnitOfMeasurePrefixInput
  # exponent applied to the base unit of measure, if it has one.
  exponent: Int!
  # Get the base unit of measure from which this unit is defined.
  base: SimpleUnitOfMeasureInput!
}

# An input for a unit of measure.   The input must either provide a list of
# factors, or name, dimension and unit conversion..
input UnitOfMeasureInput {
  # The standard symbol used for the unit of measure (e.g. "m" for "meter")
  name: String
  # The physical dimension of observations that this unit can quantify.
  dimension: PhysicalDimensionInput
  # A list of possible alternative symbols used to denote this unit of measure (e.g. "meter", "metre")
  aliases: [String]
  # Get the unit conversion from this unit of measure to the standard SI unit for this dimension.
  siUnitConversion: UnitConversionInput
  # factors of this unit.
  factors: [UnitOfMeasureFactorInput]
}

# A reference to a unit of measure prefix.  You must provide at least one of the arguments: id, name or alias.
input UnitOfMeasurePrefixInput {
  # The standard symbol used for the prefix (e.g. "k" for 1000 fold intensification).
  name: String!
  # The intensification factor implied by the prefix.
  multiplier: Float!
  # A list of possible alternative symbols used for the prefix (e.g. "kilo")
  aliases: [String]
}

# A system of units.
input SystemOfUnitsInput {
  # A short human readable name for the system of units (e.g. "mks")
  name: String!
  # A longer human readable name for the system of units (e.g. "meter-kilogram-second metric system")
  description: String
  # units of measure that belong to this system of units.
  units: [UnitOfMeasureInput]!
  # unit of measure prefixes (intensifiers) that belong to this system of units.
  prefixes: [UnitOfMeasurePrefixInput]!
}

# A PhysicalQuantityInput is used to specify a physical quantity for use in arithmetic or boolean operations.  You must provide either: an id, a name or both the magnitude and unitOfMeasureInput.
input PhysicalQuantityInput {
  # The magnitude of the physical quantity.
  magnitude: Float!
  # The unit of measure.
  unitOfMeasure: UnitOfMeasureInput!
}

# An entity mention that was found by the extraction query.
type EntityMention {
  # The length of the original mention in characters
  fromSpan: String!
  # The offset (in characters) from the start of the source string to the beginning of the mention
  fromOffset: String!
  # (DEPRECATED) the name of the entity extracted
  entityName: String!
  # (DEPRECATED) the normal form of the entity extracted
  surfaceForm: String!
  # The physical quantity that was extracted.
  entity: PhysicalQuantity!
}

# A system of units is a collection of units of measure and prefixes (intensifiers) that can be used together for quantifying and comparing measurements.   Examples of systems of units include the metric (mks system) and the imperial system.
type SystemOfUnits {
  # The unique identifier
  id: ID!
  # A short human readable name for the system of untis (e.g. "mks")
  name: String!
  # A longer human readable name for the system of units (e.g. "meter-kilogram-second metric system")
  description: String!
  # get all the units of measure that belong to this system of units, optionally filtering by physical dimension.
  units(dimensionRef: PhysicalDimensionRef): [UnitOfMeasure]
  # get all the unit of measure prefixes (intensifiers) that belong to this system of units.
  prefixes: [UnitOfMeasurePrefix]
}

# A unit of measure prefix is a symbol that can occur juxtaposed with a unit of measure to scale the magnitudes.  For example, the metric mks system uses the prefix "k" to denote a thousand fold intensification of the measured value.
type UnitOfMeasurePrefix {
  # The unique identifier
  id: ID!
  # The standard symbol used for the prefix (e.g. "k" for 1000 fold intensification).
  name: String!
  # The intensification factor implied by the prefix.
  multiplier: Float!
  # A list of possible alternative symbols used for the prefix (e.g. "kilo")
  aliases: [String]
}

# A primary (elementary/atomic) unit of measure is not a product of prefixes and/or unit of measure factors.
type SimpleUnitOfMeasure {
  # The unique identifier
  id: ID!
  # The standard symbol used for the unit of measure (e.g. "m" for "meter")
  name: String!
  # The physical dimension of observations that this unit can quantify.
  dimension: PhysicalDimension!
  # A list of possible alternative symbols used to denote this unit of measure (e.g. "meter", "metre")
  aliases: [String]
  # Get the unit conversion from this unit of measure to the standard SI unit for this dimension.
  siUnitConversion: UnitConversion!
  # Is the unit of measure unitless ?
  isUnitless: Boolean
}

# The UnitOfMeasure represents a scale for quantifying the measurement of observable physical phenomenon;  each unit of measure has a symbol that can be used to denote the used to indicate that a value has been quantified with the particular scale, a conversion factor to the standard reference scale in the mks system, and the physical dimension of the phenomenon that the unit can be used to classify.   NOTE: units of measure may be elementary, consisting of just a single symbol, prefixed by an intensifier, exponentiated to an integer power, or the product or quotient of other units of measure.   This type provides comprehension functions to deconstruct a unit of measure into its constituents.
type UnitOfMeasure {
  # The unique identifier
  id: ID!
  # The standard symbol used for the unit of measure (e.g. "m" for "meter")
  name: String!
  # The physical dimension of observations that this unit can quantify.
  dimension: PhysicalDimension!
  # A list of possible alternative symbols used to denote this unit of measure (e.g. "meter", "metre")
  aliases: [String]

  # Test if this unit of measure can be converted to the specified unit of measure.
  canBeConvertedTo(toUnitInput: UnitOfMeasureInput): Boolean!

  # Get the prefix of the unit of measure, if it has one.
  prefix: UnitOfMeasurePrefix
  # Get the exponent applied to the base unit of measure, if it has one.
  exponent: Int
  # Get the base unit of measure from which this unit is defined.  Null if this unit is elementary.
  base: UnitOfMeasure
  # Get the factors of this unit of measure if it is a product or quotient.  Empty otherwise.
  factors: [UnitOfMeasureFactor]!

  # Compute the scalar product of this unit of measure with the given prefix.
  scale(prefix: UnitOfMeasurePrefixInput): UnitOfMeasure
  # Compute the binary product of this unit of measure by the given factor.
  mul(factor: UnitOfMeasureInput!): UnitOfMeasure!
  # Compute the quotient of this unit of measure and the given divisor.
  div(divisor: UnitOfMeasureInput!): UnitOfMeasure!
  # Compute the multiplicative inverse of this unit of measure.
  reciprocal: UnitOfMeasure!

  # Compute the exponent of this unit of measure raised to the specified integer power.
  power(exponent: Int): UnitOfMeasure!

  # Is the unit of measure a integer power of some elementary unit of measure, possibly scaled by a prefix?
  isPower: Boolean
  # Is the unit of measure a elementary unit of measure scaled by a prefix?
  hasPrefix: Boolean
  # Is the unit of measure a product of multiple unit of measure factors?
  isSimple: Boolean
  # Is the unit of measure simple.  ?
  isComposite: Boolean
  # Is the unit of measure unitless ?
  isUnitless: Boolean
}

# The UnitOfMeasureFactor type represents a factorization of a unit of measure into a simple unit of measure, its prefix (if any) and its exponent.
type UnitOfMeasureFactor {
  # Get the prefix of the unit of measure, if it has one.
  prefix: UnitOfMeasurePrefix
  # Get the exponent applied to the base unit of measure, if it has one.
  exponent: Int!
  # Get the base unit of measure from which this unit is defined.
  base: SimpleUnitOfMeasure!
}

# The PhysicalDimension type represents a physical dimension (e.g. length, velocity) that can be associated with one or more units of measure.   Each physical dimension is the product (or quotient) of the seven orthogonal basis dimensions as defined by the General Conference on Weights and Measures: LENGTH (L), MASS (M), TIME (T), TEMPERATURE (ϴ), AMOUNT (N), ELECTRICAL CONDUCTANCE (J), and LUMINOUS INTENSITY (I)
type PhysicalDimension {
  # The unique identifier
  id: ID!
  # A human readable name for the physical dimension (e.g. "LENGTH")
  name: String
  # The formula of the physical dimension
  formula: String!
  # A list of alternate names for the physical dimension.
  aliases: [String]

  # Compute the binary product of this dimension and the given factor.
  mul(factor: PhysicalDimensionInput!): PhysicalDimension!
  # Compute the quotient of this dimension and the given divisor.
  div(divisor: PhysicalDimensionInput!): PhysicalDimension!
  # Compute the multiplicative inverse of this physical dimension.
  reciprocal: PhysicalDimension!

  # Compute the exponent of this unit of measure raised to the specified integer power.
  power(exponent: Int): PhysicalDimension!
}

# A PhysicalQuantity is a quantifiable observation of a physical phenomenon (aka a measurement); each physical quantity has a magnitude, representing the value of the measurement, and a unit of measure that identifies a reference scale for comparing that value with other observations.
type PhysicalQuantity {
  # The unique identifier
  id: ID!
  # The preferred string representation of the quantity (e.g. "1.0 m")
  name: String!
  # The magnitude of the physical quantity.
  magnitude: Float!
  # The unit of measure.
  unitOfMeasure: UnitOfMeasure!

  # Convert this physical quantity to the specified unit of measure.  Must be dimensionally consistent.
  convertTo(toUnitInput: UnitOfMeasureInput!, precision: Int): PhysicalQuantity
  # Test if this physical quantity can be be converted to the specified unit of measure.
  canBeConvertedTo(toUnitInput: UnitOfMeasureInput): Boolean!
  # Round this physical quantity to the specified precision.
  roundTo(precision: Int!): PhysicalQuantity!

  # Test if this physical quantity is greater than the specified physical quantity.   Must be dimensionally consistent.
  isGT(
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
  # Test if this physical quantity is greater than or equal to the specified physical quantity.   Must be dimensionally consistent.
  isGTE(
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
  # Test if this physical quantity is less than the specified physical quantity.   Must be dimensionally consistent.
  isLT(
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
  # Test if this physical quantity is less than or equal to the specified physical quantity.   Must be dimensionally consistent.
  isLTE(
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
  # Test if this physical quantity is equal to the specified physical quantity.   Must be dimensionally consistent.
  isEqual(
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!

  # Compute the additive inverse of this physical quantity.
  negate(toUnitInput: UnitOfMeasureInput, precision: Int): PhysicalQuantity!
  # Compute the binary sum of this physical quanity and the given summand.  Must be dimensionally consistent.
  add(
    summand: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the binary difference of this physical quanity and the given subtrahend.  Must be dimensionally consistent.
  sub(
    subtrahend: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!

  # Compute the scalar multiple of this physical quantity and the provided scalar value.
  scale(
    scalar: Float!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the binary product of this physical quantity by the given factor.
  mul(
    factor: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the quotient of this physical quantity and the given divisor.
  div(
    divisor: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the multiplicative inverse of this physical quantity.
  reciprocal(toUnitInput: UnitOfMeasureInput, precision: Int): PhysicalQuantity!

  # Compute the exponent of this physical quantity raised to the specified integer power.
  power(
    exponent: Int!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the larger of the physical quantity or the argument.
  max(
    argument: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the smaller of the physical quantity or the argument.
  min(
    argument: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
}

# Unit conversion can be performed by using the convertTo function and providing a dimensinally consistent unit of measure.    UnitConversion data can be used to inspect the parameters for a for a unit of measure conversion.   Unit conversions are of the form: y=multiplier x + offset
type UnitConversion {
  # The scaling factor used by the unit of measure conversion.
  multiplier: Float!
  # The offset used by the unit of measure conversion.
  offset: Float!
  # Compute the inverse of a unit of measure conversion.
  inverse: UnitConversion
}

# The physical quantity service provides methods for querying systems of units, extracting physical quantities from text and performing arithmetic and comparison operations on physical quantities.
type Query {
  # information about the service
  info: Info!
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  systemsOfUnits: [SystemOfUnits]
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  systemOfUnits(systemRef: SystemOfUnitsRef): SystemOfUnits
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  unitOfMeasurePrefixes: [UnitOfMeasurePrefix]
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  unitOfMeasurePrefix(
    prefixRef: UnitOfMeasurePrefixRef!
  ): UnitOfMeasurePrefix
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  physicalDimensions: [PhysicalDimension]
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  physicalDimension(dimensionRef: PhysicalDimensionRef!): PhysicalDimension
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  unitsOfMeasure: [UnitOfMeasure]
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  unitOfMeasure(unitRef: UnitOfMeasureRef!): UnitOfMeasure
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  physicalQuantities: [PhysicalQuantity]
  # (DEPRECATED) - this field will be replaced with standardized CRUD operations in an upcomming release.
  physicalQuantity(input: PhysicalQuantityRef): PhysicalQuantity

  # Test if the given string is a surface form for a physical quantity.
  isSurfaceForm(
    source: String!
    systemInputs: [SystemOfUnitsInput]
    dimensionInputs: [PhysicalDimensionInput]
  ): Boolean!
  # Extract entity mentions from the given string.
  extract(
    source: String!
    systemInputs: [SystemOfUnitsInput]
    dimensionInputs: [PhysicalDimensionInput]
  ): [EntityMention]
    # Extract entity mentions from an array of strings.
  extractBatch(
    sources: [String]!
    systemInputs: [SystemOfUnitsInput]
    dimensionInputs: [PhysicalDimensionInput]
  ): [[EntityMention]]
  # Extract a physical quantity from the given string.
  parse(
    source: String!
    systemInputs: [SystemOfUnitsInput]
    dimensionInputs: [PhysicalDimensionInput]
  ): PhysicalQuantity

  # (DEPRECATED) - use PhysicalQuantity:roundTo instead.
  roundTo(input: PhysicalQuantityInput, precision: Int!): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:convertTo instead.
  convertTo(
    input: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput!
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:canBeConvertedTo instead.
  canBeConvertedTo(
    input: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
  ): Boolean!

  # (DEPRECATED) - use PhysicalQuantity:negate instead
  negate(
    input: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:add instead
  add(
    input: PhysicalQuantityInput
    summand: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:sub instead
  sub(
    input: PhysicalQuantityInput
    subtrahend: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the n-ary sum of a given list of physical quantities.  Must be dimensionally consistent.
  sum(
    summands: [PhysicalQuantityInput]!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!

  # (DEPRECATED) - use PhysicalQuantity:reciprocal instead
  reciprocal(
    input: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:scale instead
  scale(
    input: PhysicalQuantityInput
    scalar: Float!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:mul instead
  mul(
    input: PhysicalQuantityInput
    factor: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # (DEPRECATED) - use PhysicalQuantity:add instead
  div(
    input: PhysicalQuantityInput
    divisor: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the n-ary product of a given list of physical quantities.
  product(
    factors: [PhysicalQuantityInput]!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!

# (DEPRECATED) - use PhysicalQuantity:power instead
  power(input: PhysicalQuantityInput,
      exponent: Int,
      toUnitInput: UnitOfMeasureInput
      precision: Int
  ): PhysicalQuantity!

# (DEPRECATED) - use PhysicalQuantity:max instead
  max(
    input: PhysicalQuantityInput
    argument: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
# (DEPRECATED) - use PhysicalQuantity:min instead
  min(
    input: PhysicalQuantityInput
    argument: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the maximum of a given list of physical quantities.   Must be dimensionally consistent.
  maximum(
    inputs: [PhysicalQuantityInput]!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the minimum of a given list of physical quantities.   Must be dimensionally consistent.
  minimum(
    inputs: [PhysicalQuantityInput]!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!
  # Compute the average of a given list of physical quantities.   Must be dimensionally consistent.
  average(
    inputs: [PhysicalQuantityInput]!
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): PhysicalQuantity!

# (DEPRECATED) - use PhysicalQuantity:isGT instead
  isGT(
    input: PhysicalQuantityInput
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
# (DEPRECATED) - use PhysicalQuantity:isGTE instead
  isGTE(
    input: PhysicalQuantityInput
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
# (DEPRECATED) - use PhysicalQuantity:isLT instead
  isLT(
    input: PhysicalQuantityInput
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
# (DEPRECATED) - use PhysicalQuantity:isLTE instead
  isLTE(
    input: PhysicalQuantityInput
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
# (DEPRECATED) - use PhysicalQuantity:isEqual instead
  isEqual(
    input: PhysicalQuantityInput
    rhs: PhysicalQuantityInput
    toUnitInput: UnitOfMeasureInput
    precision: Int
  ): Boolean!
}

schema {
  query: Query
}
```

## Examples

**NOTE on Fragments**: Many of the examples in this document use graphQL fragments to simplify the output specifications.   Definitions of the graphQL fragments can be found in the appendix.  

**NOTE on Variables**:  Some examples require execution of a sequence of queries, storing intermediate results in variables to be passed to later stages.   For clarity, we name the earlier stages of computation with the same name as the variable to which they are bound.   For example:

```text
query FOO{ foo{ ... FooFragment }}
query BAR( $FOO: FooInput ){ ... BarFragment }
```
Means, execute the query FOO, storing the result in the variable $FOO, and then call the query BAR.

### Physical Quantities

| Example | How do I get the normalized surface form of a physical quantity? |
| :--- | :--- |
| Info | The physical quantity service automatically normalizes the surface form of physical quantities. Use the name property of the physical quantity type to get the normalized surface form of a physical quantity. The example below demonstrates how to get the normalized surface form of a physical quantity parsed from text. |
| Query | query EXAMPLE1{parse(source:"ten psi"){ name }} |
| Response | { "data": { "parse": { "name": "10.0 lb/in²" }}} |

| Example | How do I get the magnitude \(numeric value\) of a physical quantity? |
| :--- | :--- |
| Info | Use the magnitude property of the physical quantity type to get the numeric value of a physical quantity. The example below demonstrates how to get the numeric value of a physical quantity parsed from text. |
| Query | query EXAMPLE2{parse(source:"ten psi"){ magnitude }} |
| Response | { "data": { "parse": { "magnitude": 10 }}} |

| Example | How do I get the unit of measure of a physical quantity? |
| :--- | :--- |
| Info | Use the unitOfMeasure property of the physical quantity type to get the unit of measure of a physical quantity. The example below demonstrates how to get the unit of measure of a physical quantity parsed from text. |
| Query | query EXAMPLE3{parse(source:"ten psi"){ unitOfMeasure{ name }}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "name": "lb/in²" }}}} |

### Unit Of Measure

| Example | How can I see all the available units of measure? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL available units of measure. Instead, we can use the unitsOfMeasure query to find all the units of measure that have been explicitly defined. The physical quantity service comes out of the box with explicit definitions for the commonly used units of measure for both the MKS and Imperial systems of units. |
| Query | query EXAMPLE4{unitsOfMeasure { name }} |
| Result | { "data": { "unitsOfMeasure": \[{"name": "C"}, {"name": "m"}, { "name": "km"}, { "name": "D" }, .. AND MANY MORE \] }} |

| Example | How can I see all the available units of measure for a particular physical dimension? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL available units of measure. |

| Example | How do I get the normalized surface form of a unit of measure? |
| :--- | :--- |
| Info | The physical quantity service automatically normalizes the surface form of units of measure. Use the name property of the UnitOfMeasure type to get the normalized surface form. The example below demonstrates how to get the normalized surface form of a unit of measure from a physical quantity parsed from text. |
| Query | query EXAMPLE5{parse(source: "9.8 kilogram \* meter / second squared") {  unitOfMeasure {  name}}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "name": "kg·m/s²"}}}} |

| Example | How do I get the aliases for a unit of measure? |
| :--- | :--- |
| Info | Use the aliases property of the UnitOfMeasure type to get the aliases for the unit of measure. Any of the returned aliases could be used interchangeably with the preferred denotation. The example below demonstrates how to get the aliases for a unit of measure from a physical quantity parsed from text. |
| Query | query EXAMPLE6{unitOfMeasure(unitRef:{name:"mi/hr"}) {  aliases}} |
| Response | { "data": { "unitOfMeasure": { "aliases": \[ "mph"\]}}} |

| Example | How do I get the physical dimension of a unit of measure? |
| :--- | :--- |
| Info | Use the dimension property of the UnitOfMeasure type to get its physical dimension. The example below demonstrates how to get the physical dimension form of a unit of measure from a physical quantity parsed from text. |
| Query | query EXAMPLE7{unitOfMeasure(unitRef:{name:"N"}){dimension{name}}} |
| Response | { "data": { "unitOfMeasure": { "dimension": { "name": "FORCE"}}}} |

### Unit Of Measure Prefix

| Example | How can I see all the available unit of measure prefixes? |
| :--- | :--- |
| Info | Use the unitOfMeasurePrefixes query to find all the unit of measure prefixes. The physical quantity service comes out of the box with the metric system prefixes. |
| Query | query EXAMPLE8{unitOfMeasurePrefixes{  name}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}} |

| Example | How do I get the preferred symbol for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the name property of the UnitOfMeasurePrefix to get the preferred symbol for the prefix. The example below gets the preferred symbol for all the unit of measure prefixes in the system: |
| Query | query EXAMPLE9{unitOfMeasurePrefixes{  name}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}} |

| Example | How do I get the aliases for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the aliases property of the UnitOfMeasurePrefix to get all the aliases for the unit of measure prefix. The example below gets the aliases for all the unit of measure prefixes in the system: |
| Query | query EXAMPLE10{unitOfMeasurePrefixes{  aliases}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "aliases": \[ "f", "fempto"\]}, { "aliases": \[ "p", "pico"\]}, { "aliases": \[ "n", "nano"\]}, { "aliases": \[ "μ", "u", "micro"\]}, ... AND MANY MORE\]}} |

| Example | How do I get the scaling factor for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the multiplier property of the UnitOfMeasurePrefix to get all the scaling factor for the unit of measure prefix. The example below gets the multipliers for all the unit of measure prefixes in the system: |
| Query | query EXAMPLE11{unitOfMeasurePrefixes{  multiplier}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "multiplier": 1e-15}, { "multiplier": 1e-12}, { "multiplier": 1e-9}, { "multiplier": 0.000001},  ... AND MANY MORE\]}} |

### Physical Dimensions

| Example | How can I see all the available physical dimensions? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of physical dimensions using multiplication, division and exponentiation, It is NOT possible to see ALL available physical dimensions. Instead, we can use the physicalDimensions query to find all the physical dimensions that have been explicitly defined. The physical quantity service comes out of the box with over 60 of the most commonly occurring physical dimensions in engineering and scientific literature. |
| Query | query EXAMPLE12{physicalDimensions {  name aliases formula}} |
| Response | { "data": { "physicalDimensions": \[ { "name": "VELOCITY", "aliases": \[\], "formula": "L¹T⁻¹"}, { "name": "ELECTRICAL\_RESISTANCE", "aliases": \[\], "formula": "L²M¹T⁻³J⁻²"}, { "name": "ABASEMENT", "aliases": \[\], "formula": "L¹T¹"}, { "name": "MOMENTUM", "aliases": \[\], "formula": "L¹M¹T⁻¹" ... AND MANY  MORE},\]}} |

| Example | How do I look up a physicalDimension by its name, formula or alias? |
| :--- | :--- |
| Info | Use the physicalDimension query to search for a physical dimension based on its name, formula or alias. The example below finds a physical dimension by name: |
| Query | query EXAMPLE13{physicalDimension(dimensionRef:{name:"ELECTRICAL\_CURRENT"}){  formula}} |
| Response | { "data": { "physicalDimension": { "formula": "J¹"}}} |

| Example | How do I get the preferred name of a physical dimension? |
| :--- | :--- |
| Info | Use the name field of the PhysicalDimension type to get the preferred name for a physical dimension. The example below gets the preferred name of all the explicitly defined physical dimensions: |
| Query | query EXAMPLE14{ physicalDimensions{name}} |
| Response | { "data": { "physicalDimensions": \[ { "name": "VELOCITY"}, { "name": "ELECTRICAL\_RESISTANCE"}, { "name": "ABASEMENT"}, { "name": "MOMENTUM"}, ... AND MANY MORE\]}} |

| Example | How can I get the aliases for a physical dimension? |
| :--- | :--- |
| Info | Use the name field of the PhysicalDimension type to get the preferred name for a physical dimension. The example below gets the aliases of all the explicitly defined physical dimensions having the formula "L²M¹T⁻²": |
| Query | query EXAMPLE15{physicalDimension(dimensionRef:{formula:"L²M¹T⁻²"}){  aliases}} |
| Response | { "data": { "physicalDimension": { "aliases": \[ "ENERGY" "WORK", "TORQUE"\],}}} |

| Example | How can I get the formula for a physical dimension? |
| :--- | :--- |
| Info | Use the name formula of the PhysicalDimension type to get the formula for a physical dimension. The example below gets the formula of the explicitly defined physical dimension of "MOLAR\_HEAT\_CAPACITY": |
| Query | query EXAMPLE16{physicalDimension(dimensionRef:{name:"MOLAR\_HEAT\_CAPACITY"}){  formula}} |
| Response | { "data": { "physicalDimension": { "formula": "L²M¹T⁻²ϴ⁻¹N⁻¹"}}} |

### Systems Of Units

| Example | How can I get a list of all the systems of units? |
| :--- | :--- |
| Info | Use the systemsOfUnits query to get a list of all the systems of units. For example the query below gets a list of all the names and descriptions of all the systems of units. |
| Query | query EXAMPLE17{systemsOfUnits{  name, description}} |
| Response | {"data": { "systemsOfUnits":\[{"name": "mks","description": "mks (meter-kilogram-second) system" },{"name": "Imperial","description": "Imperial (American) Unit System"},{"name": "oil and gas","description": "Oil and Gas domain specific units of measure"}]}} |

| Example | How can I see all the units of measure in a system of units? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL the units of measure in a system of units. Instead, you can use the units field of the SystemOfUnits type to get the list of explicitly defined units of measure that occur in that system of units. |
| Query | query EXAMPLE18{systemOfUnits(systemRef:{name:"Imperial"}){  units{ name}}} |
| Response | { "data": { "systemOfUnits": { "units": \[ { "name": "ft"}, { "name": "in"}, { "name": "mi"}, { "name": "yd"}\]}}} |

| Example | How can I see all the unit of measure prefixes in a system of units? |
| :--- | :--- |
| Info | Use the prefixes field of the SystemOfUnits type to get the list of unit of measure prefixes in that system of units. The example below demonstrates how to find all the unit of measure prefixes in the mks unit system: |
| Query | query EXAMPLE19{systemOfUnits(systemRef:{name:"mks"}){  prefixes{  name}}} |
| Response | { "data": { "systemOfUnits": { "prefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}}} |

### Parsing

| Example | How do I convert that string to a physical quantity? |
| :--- | :--- |
| Info | Use the parse query to convert a string into a physical quantity. The parse query can be used when you have a single string that is a surface form of an entity mention -- the parse query will throw an error if the provided string is not a surface form for some physical quantity. |
| Query | EXAMPLE20{parse(source:"400 psi"){  name}} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

| Example | How do I convert the string to a physical quantity only if its unit of measure is in a particular system of units? |
| :--- | :--- |
| Info | Use the optional systemRefs parameter to specify either the id or name of the required systems of units. If no systemRefs parameter is provided, the parser uses all available systemsOfUnits. |
| Query | query IMPERIAL{systemOfUnits\(systemRef:{name:"Imperial"}\){ ... SystemOfUnitsFragment}}<br>query EXAMPLE21($IMPERIAL:SystemOfUnitsInput){ parse(source:"400 psi",systemInputs:[$IMPERIAL]){  name}} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

| Example | How do I convert the string to a physical quantity only if it has a specific physical dimension? |
| :--- | :--- |
| Info | Use the optional dimensionInput parameter to specify either the name or formula of the required physical dimension. If no dimensionRef parameter is provided, the parser admits all physical dimensions. |
| Query | query LENGTH{physicalDimension\(dimensionRef:{name:"LENGTH"}\){ ... PhysicalDimensionFragment }}<br>query EXAMPLE22($LENGTH:PhysicalDimensionInput){ parse(source:"400 meters",dimensionInputs:[$LENGTH]){  name }} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

### Extraction

| Example | You have a list of strings, each which may contain zero or many physical quantities. How do you extract all the physical quantities from the source text? |
| :--- | :--- |
| Info | Use the extract query to get all the entity mentions from the text. The extract method can be used to find entity mentions in a list of string literals, or the field of a kind. The extractor will find all the physical quantity mentions in each string \(or instance of a field\) and return a list of the results. |
| Query | query EXAMPLE23{extractBatch(sources: [ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."]) {  entity {  name}} } |
| Result | { "data": { "extractBatch": \[ \[{ "entity": { "name": "350.0 mi/hr"}}, { "entity": { "name": "3000 m"}}\], \[{ "entity": { "name": "7500.0 ft"}}\]\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. How do extract all the physical quantities from the source text only if their unit of measure is in the Imperial unit system? |
| :--- | :--- |
| Info | Use the optional systemInputs parameter to specify either the id or name of the required systems of units. If no systemInputs parameter is provided, the extractor uses all available systemsOfUnits. |
| Query | query IMPERIAL{systemOfUnits\(systemRef:{name:"Imperial"}\){ ... SystemOfUnitsFragment}}<br>query EXAMPLE24($IMPERIAL:SystemOfUnitsInput){ extractBatch(sources: ["the airspeed of the plane was 350 mph as it passed through 3000 meters.","The minimum runway length is 7500 feet."], systemInputs:[$IMPERIAL]) {  entity {  name}}} |
| Response | { "data": { "extractBatch": \[ { "entity": { "name": "350.0 mi/hr"}}, { "entity": { "name": "7500.0 ft"}}\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. How do extract all the physical quantities from the source text only if they have dimensions of length? |
| :--- | :--- |
| Info | Use the optional dimensionInputs parameter to specify either the name or formula of the required physical dimension. If no dimensionInputs parameter is provided, the extractor admits all physical dimensions. |
| Query | query EXAMPLE25{extractBatch(sources: [ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."], dimensionInputs:[{name:"LENGTH",formula:"L¹"}]) {  entity {  name}}} |
| Response | { "data": { "extractBatch": \[ { "entity": { "name": "3000.0 m"}}, { "entity": { "name": "7500.0 ft"}}\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. After extracting the entity mentions, how do you know where they occur in the source text? |
| :--- | :--- |
| Info | The fromKind, fromField, fromInstance, and fromString fields of the EntityMention type provide locators to the source text. The fromOffset field of the entity EntityMention type provides the distance from the start of the string \(in characters\) to the start of the entity mention. The fromSpan field of the EntityMention type provides the length of the entity mention \(in characters\). |
| Query | query LENGTH{physicalDimension\(dimensionRef:{name:"PRESSURE"}\){ ... PhysicalDimensionFragment }}<br>query EXAMPLE26($LENGTH:PhysicalDimensionInput){ extractBatch(sources: [ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."], dimensionInputs:[$LENGTH] ) {  entity {  name} fromSpan, fromOffset}} |
| Response | { "data": { "extractBatch": \[ \[ { "entity": { "name": "3000.0 m"}, "fromSpan": "12", "fromOffset": "59"}\], \[{ "entity": { "name": "7500.0 ft"}, "fromSpan": "10", "fromOffset": "29"}\]}} |

## Conversions

| Example | How do I convert a physical quantity to a different unit of measure? |
| :--- | :--- |
| Info | You can use the "convertTo" query, "convertTo" field, or "convertTo" argument to convert a physical quantity to a specific unit of measure. The examples below show examples of how to use each method to convert a pressure from atmospheres to gauge pressure. |
| using nested calls | query PSI{ unitOfMeasure(unitRef:{name:"lb/in²"}){ ...UnitOfMeasureFragment }}<br>query EXAMPLE27($PSI:UnitOfMeasureInput!){parse(source:"100 mbar"){ convertTo(toUnitInput:$PSI){ roundTo(precision:2){name}}}} |
| using arguments | query PSI{ unitOfMeasure(unitRef:{name:"lb/in²"}){ ...UnitOfMeasureFragment }}<br>query EXAMPLE28($PSI:UnitOfMeasureInput!){convertTo(input:{parseFrom:"100 mbar"},toUnitInput:$PSI,precision:2){ name}} |
| Response | { "data": { "convertTo": { "name": "1.5 lb/in²" }}} |

| Example | How can I tell if a physical quantity can be converted to a specific unit of measure? |
| :--- | :--- |
| Info | Use the canBeConverted to field of the UnitOfMeasure type to test if two units are interconvertible. The following query demonstrates how to test if a physical quantity can be converted to units of "psi' |
| Query | query PSI{ unitOfMeasure(unitRef:{name:"lb/in²"}){ ...UnitOfMeasureFragment }}<br>query EXAMPLE29($PSI:UnitOfMeasureInput!){canBeConvertedTo(input:{parseFrom:"100 mbar"}, toUnitInput:$PSI)} |
| Response | { "data": { "canBeConvertedTo": true  } } |

### Arithmetic

| Example | Add physical quantities with different dimensions |
| :--- | :--- |
| Info | The physical quantity service will perform the necessary unit conversions for you, so long as the units are dimensionally consistent |
| Query | query EXAMPLE30{sum(summands: [{parseFrom: "12 inch"}, {parseFrom: "1 foot"}, {parseFrom: "1/3 yard"}], precision: 2) {  name}} |
| Response | { "data": { "sum": { "name": "36.0 in"}}} |

| Example | Multiplication and division |
| :--- | :--- |
| Info | When you pass arguments that have different units or physical dimensions, computer algebra on units of measure and physical dimensions automatically constructs the correct unit of measure for the returned physical quantity |
| Query | query EXAMPLE31{product(factors: [{parseFrom: "12 inch"}, {parseFrom: "1 Hz"}, {parseFrom: "1/3 acre"}], precision: 2) {  name,  unitOfMeasure {  dimension {  formula}}}} |
| Response | { "data": { "product": { "name": "4.0 Hz·acre·in", "unitOfMeasure": { "dimension": { "formula": "L³T⁻¹"}}}}} |

### Size Comparisons

| Example | Comparing size without converting units |
| :--- | :--- |
| Info | The physical quantity service will perform the necessary unit conversions for you, as long as the units are dimensionally consistent |
| Query | query REFERENCE{ parse(source:"35 cm") { ... PhysicalQuantityFragment }}<br>query EXAMPLE32($REFERENCE:PhysicalQuantityInput!){extractBatch(sources: ["1 inch","1 foot","1 yard"]) { entity{  isGT(rhs:$REFERENCE)}}} |
| Response | { "data": { "extractBatch": [ [ { "entity": { "isGT": false } } ], [ { "entity": { "isGT": false } }], [{ "entity": {  "isGT": true } } ] ] } }|

# Appendix
## GraphQL Fragments
```text
fragment PhysicalDimensionFragment on PhysicalDimension {
  name
  aliases
  formula
}

fragment UnitOfMeasurePrefixFragment on UnitOfMeasurePrefix {
  name
  aliases
  multiplier
}

fragment UnitOfMeasureFragment on UnitOfMeasure {
  name
  aliases
  factors {
    base {
      name
      aliases
      dimension { ... PhysicalDimensionFragment }
      siUnitConversion { multiplier offset }
    }
    prefix { ... UnitOfMeasurePrefixFragment }
    exponent
}}

fragment PhysicalQuantityFragment on PhysicalQuantity{
  magnitude
  unitOfMeasure{ ... UnitOfMeasureFragment }
}

fragment SystemOfUnitsFragment on SystemOfUnits{
  name
  description
  units{ ... UnitOfMeasureFragment }
  prefixes{ ... UnitOfMeasurePrefixFragment }   
}
```
