# Physical Quantity

## Physical Quantity

This service is used for extracting physical quantities \( a.k.a. measured quantities\) from text, and performing dimensionally safe arithmetic, unit conversions and comparisons with them. This service uses a proprietery symbolic computing engine built on top of Stanford CoreNLP's named entity recognition library \([https://nlp.stanford.edu/software/](https://nlp.stanford.edu/software/)\).

This query extracts pressure measurements from the sample sentence, converts them into head \(in feet of sea water\) and checks to see if the result is greater than 100 feet:

```text
{
  extract(sources: ["The back-pressure should not exceed 3 atm."]) {
    entity {
      div(divisor: {parseFrom: "1030 kg/m³"}){
        div(divisor:{parseFrom:"32 ft/s²"}, toUnitRef:{ name:"ft"}, precision:3) {
          name
          isGTE( rhs: {parseFrom:"100 feet"})
        }
      }
    }
  }
}
```

Queries of this kind facilitate equational reasoning with real world measurements, and can be used to augment the engineering knowledge models. Specifically, this service facilitates equational and graph based reasoning about physical quantities, units of measure, physical dimensions and systems of units.

## Assumptions

1. Entity mentions follow standard notational conventions for products, quotients and exponentials of units of measure
2. Although aliases are allowed, no unit of measure that is not a product, power or quotient can consist of more than one token.  For example: "1 Astronomical unit" would be invalid, but "1 AU" would be acceptable

## Warnings

1. The service uses a constructive pattern matching algorithm to parse physical quantities from text.    Acronyms, abreviations and part numbers can easily be misidentified as units of measure.    Some data cleansing may be required to get optimal performance.

## Schema

```ruby
# A reference to a system of units.   You must provide at least one of the arguments: id or name.
input SystemOfUnitsRef{
    # The unique identifier.
    id: ID
    # A human readable name for the system of units.
    name: String
}

# A reference to a unit of measure.  You must provide at least one of the arguments: id, name or alias.
input UnitOfMeasureRef{
    # The unique identifier.
    id: ID
    # The standard symbolic representation (e.g. "m" )
    name: String
    # An alternative symbolic representation (e.g. "meter")
    alias: String
}

# A reference to a unit of measure prefix.  You must provide at least one of the arguments: id, name or alias.
input UnitOfMeasurePrefixRef{
    # The unique identifier.
    id: ID
    # The standard symbolic representation (e.g. "k")
    name: String
    # An alternative symbolic representation (e.g. "kilo")
    alias: String
}

# Reference to a physical dimension.   You must provide at least one of the arguments: id, name, formula
input PhysicalDimensionRef{
    # The unique identifier
    id: ID
    # The name for the dimension ( e.g. "VELOCITY")
    name: String
    # The standard formula for the dimension (e.g. "L¹T⁻¹")
    formula: String
}

type EntityMention {
   fromKindId: ID
   fromFieldId: ID
   fromInstanceId: ID
   fromSpan: String!
   fromOffset: String!
   toKindName: String!
   surfaceForm: String!
   entity: PhysicalQuantity!
   toInstance: String!
}


# A system of units is a collection of units of measure and prefixes (intensifiers) that can be used together for quantifying and comparing measurements.   Examples of systems of units include the metric (mks system) and the imperial system.
type SystemOfUnits{
    # The unique identifier
    id: ID!
    # A short human readable name for the system of untis (e.g. "mks")
    name: String!
    # A longer human readable name for the system of units (e.g. "meter-kilogram-second metric system")
    description: String!
    # get all the units of measure that belong to this system of units, optionally filtering by physical dimension.
    units( dimensionRef: PhysicalDimensionRef ): [UnitOfMeasure]
    # get all the unit of measure prefixes (intensifiers) that belong to this system of units.
    prefixes: [UnitOfMeasurePrefix]
    # The unit regexes
    unitRegexes: String
    # The prefix regexes
    prefixRegexes: String
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
    # A regex for extracting the unit of measure (if applicable)
    regex: String

    # Find all the systems of units in the store that include this unit of measure.
    systemsOfUnits: [SystemOfUnits]
    # Find all the physical quantities in the store that have this unit of measure.
    physicalQuantities: [SystemOfUnits]

    # Test if this unit of measure can be converted to the specified unit of measure.
    canBeConvertedTo( toUnitRef: UnitOfMeasureRef ): Boolean!
    # Get the unit conversion from this unit of measure to the standard SI unit for this dimension.
    siUnitConversion: UnitConversion
    # Get the unit conversion from this unit of measure to the specified unit of measure.
    unitConversion( toUnitRef: UnitOfMeasureRef! ): UnitConversion

    # Get the prefix of the unit of measure, if it has one.
    prefix: UnitOfMeasurePrefix
    # Get the exponent applied to the base unit of measure, if it has one.
    exponent: Int
    # Get the base unit of measure from which this unit is defined.  Null if this unit is elementary.
    base: UnitOfMeasure
    # Get the factors of this unit of measure if it is a product or quotient.  Empty otherwise.
    factors: [UnitOfMeasure]

    # Compute the scalar product of this unit of measure with the given prefix.
    scale( prefix: UnitOfMeasurePrefixRef ): UnitOfMeasure
    # Compute the binary product of this unit of measure by the given factor.
    mul( factor: UnitOfMeasureRef! ): UnitOfMeasure!
    # Compute the quotient of this unit of measure and the given divisor.
    div( divisor: UnitOfMeasureRef! ): UnitOfMeasure!
    # Compute the multiplicative inverse of this unit of measure.
    reciprocal: UnitOfMeasure!

    # Compute the exponent of this unit of measure raised to the specified integer power.
    power( exponent: Int ): UnitOfMeasure!
}

# The PhysicalDimension type represents a physical dimension (e.g. length, velocity) that can be associated with one or more units of measure.   Each physical dimension is the product (or quotient) of the seven orthogonal basis dimensions as defined by the General Conference on Weights and Measures: LENGTH (L), MASS (M), TIME (T), TEMPERATURE (ϴ), AMOUNT (N), ELECTRICAL CONDUCTANCE (J), and LUMINOUS INTENSITY (I)
type PhysicalDimension {
    # The unique identifier
    id: ID!
    # A human readable name for the physical dimension (e.g. "LENGTH")
    name: String!
    # The formula of the physical dimension
    formula: String!
    # A list of alternate names for the physical dimension.
    aliases: [String]

    # Search the data store for all the units of measure having this dimension.
    unitsOfMeasure: [UnitOfMeasure]
    # Search the data store for all the physical quantities having this dimension, optionally filtering by unit of measure.
    physicalQuantities( unitRef: UnitOfMeasureRef ): [SystemOfUnits]

    # Compute the binary product of this dimension and the given factor.
    mul( factor: PhysicalDimensionRef! ): PhysicalDimension!
    # Compute the quotient of this dimension and the given divisor.
    div( divisor: PhysicalDimensionRef! ): PhysicalDimension!
    # Compute the multiplicative inverse of this physical dimension.
    reciprocal: PhysicalDimension!

    # Compute the exponent of this unit of measure raised to the specified integer power.
    power( exponent: Int ): PhysicalDimension!
}

# A PhysicalQuantityInput is used to specify a physical quantity por use in arithmetic or boolean operations.  You must provide either: an id, a name or both the magnitude and unitOfMeasureRef.
input PhysicalQuantityInput {
    # Search the data store for an instance with this id.
    id: ID
    # Search the data store for an instance with this surface form.
    name: String
    # Parse an instance from this string using an optional list of system of units
    parseFrom: String
    systemRefs: [SystemOfUnitsRef]
    # Construct an instance with this magnitude and unit of measure.
    magnitude: Float
    unitRef: UnitOfMeasureRef
}

# A PhysicalQuantity is a quantifiable observation of a physical phenomenon (aka a measurement); each physical quantity has a magnitude, representing the value of the measurement, and a unit of measure that identifies a reference scale for comparing that value with other observations.
type PhysicalQuantity{
    # The unique identifier
    id: ID!
    # The preferred string representation of the quantity (e.g. "1.0 m")
    name: String!
    # The magnitude of the physical quantity.
    magnitude: Float!
    # The unit of measure.
    unitOfMeasure: UnitOfMeasure!

    # Convert this physical quantity to the specified unit of measure.  Must be dimensionally consistent.
    convertTo( toUnitRef: UnitOfMeasureRef!, precision: Int ): PhysicalQuantity
    # Test if this physical quantity can be be converted to the specified unit of measure.
    canBeConvertedTo( toUnitRef: UnitOfMeasureRef ): Boolean!
    # Round this physical quantity to the specified precision.
    roundTo( precision: Int! ): PhysicalQuantity!

    # Test if this physical quantity is greater than the specified physical quantity.   Must be dimensionally consistent.
    isGT( rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if this physical quantity is greater than or equal to the specified physical quantity.   Must be dimensionally consistent.
    isGTE( rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if this physical quantity is less than the specified physical quantity.   Must be dimensionally consistent.
    isLT( rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if this physical quantity is less than or equal to the specified physical quantity.   Must be dimensionally consistent.
    isLTE( rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if this physical quantity is equal to the specified physical quantity.   Must be dimensionally consistent.
    isEqual( rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!

    # Compute the additive inverse of this physical quantity.
    negate( toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!
    # Compute the binary sum of this physical quanity and the given summand.  Must be dimensionally consistent.
    add( summand: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
    # Compute the binary difference of this physical quanity and the given subtrahend.  Must be dimensionally consistent.
    sub( subtrahend: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!

    # Compute the scalar multiple of this physical quantity and the provided scalar value.
    scale( scalar: Float!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
    # Compute the binary product of this physical quantity by the given factor.
    mul( factor: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
    # Compute the quotient of this physical quantity and the given divisor.
    div( divisor: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
    # Compute the multiplicative inverse of this physical quantity.
    reciprocal( toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!

    # Compute the exponent of this physical quantity raised to the specified integer power.
    power( exponent: Int!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
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
type Query{
    # Get all the systems of units defined in the data store.
    systemsOfUnits: [SystemOfUnits]
    # Search the data store for a system of units that matches the given criteria.
    systemOfUnits( systemRef: SystemOfUnitsRef ): SystemOfUnits
    # Get all the unit of measure prefixes defined in the data store.
    unitOfMeasurePrefixes: [UnitOfMeasurePrefix]
    # Search the data store for a unit of measure prefix that matches the given criteria.
    unitOfMeasurePrefix( prefixRef: UnitOfMeasurePrefixRef! ): UnitOfMeasurePrefix
    # Get all the physical dimensions in the data store.
    physicalDimensions:[PhysicalDimension]
    # Search the data store for a physical dimension that matches the given criteria.
    physicalDimension( dimensionRef: PhysicalDimensionRef! ): PhysicalDimension
    # Search the data store for all the units of measure, optionally specifying search criteria.
    unitsOfMeasure( dimensionRef: PhysicalDimensionRef, systemRef: SystemOfUnitsRef ):[UnitOfMeasure]
    # Search the data store for a unit of mesure matching the given criteria.
    unitOfMeasure( unitRef: UnitOfMeasureRef! ): UnitOfMeasure
    # Search the data store for all the physical quantities, optionally specifying search criteria.
    physicalQuantities( dimensionRef: PhysicalDimensionRef, unitRef: UnitOfMeasureRef ):[PhysicalQuantity]
    # Search the data store for a unit of mesure matching the given criteria.
    physicalQuantity( input: PhysicalQuantityInput! ): PhysicalQuantity


    # Test if the given string is a surface form for a physical quantity.
    isSurfaceForm( source: String!, systemRefs: [SystemOfUnitsRef], dimensionRef: PhysicalDimensionRef ): Boolean!
    # Extract entity mentions from the given string.
    # In the future "sources" will be deprecated and replaced by a single source
    extract( kindId: ID, fieldId: ID, instanceIds: [ID], sources: [String]!, systemRefs: [SystemOfUnitsRef], dimensionRef: PhysicalDimensionRef ): [EntityMention]
    # Extract a physical quantity from the given string.
    parse( source:String!, systemRefs: [SystemOfUnitsRef], dimensionRef:PhysicalDimensionRef ): PhysicalQuantity

    # Get the parameters for the unit conversion between the two specified units of measure.
    unitConversion( fromUnitRef: UnitOfMeasureRef!, toUnitRef: UnitOfMeasureRef! ): UnitConversion
    # Round the given physical quantity to the specified precision.
    roundTo( input: PhysicalQuantityInput!, precision: Int! ): PhysicalQuantity!
    # Convert the given physical quantity to the specified unit of measure.
    convertTo( input: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef!, precision: Int ): PhysicalQuantity!

    # Compute the additive inverse of the given physical quantity.
    negate( input: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!
    # Compute the binary sum of two given physical quantities.  Must be dimensionally consistent.
    add(  input: PhysicalQuantityInput!, summand: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int):PhysicalQuantity!
    # Compute the binary difference of two given physical quantities.  Must be dimensionally consistent.
    sub(  input: PhysicalQuantityInput!, subtrahend: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int):PhysicalQuantity!
    # Compute the n-ary sum of a given list of physical quantities.  Must be dimensionally consistent.
    sum( summands: [PhysicalQuantityInput]!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!

    # Compute the multiplicative inverse of the given physical quantity.
    reciprocal( input: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!
    # Scale the given physical quantity by the specified scalar.
    scale( input: PhysicalQuantityInput!, scalar: Float!, toUnitRef: UnitOfMeasureRef, precision: Int ): PhysicalQuantity!
    # Compute the binary product of two given physical quantities.
    mul(  input: PhysicalQuantityInput!, factor: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int):PhysicalQuantity!
    # Compute the binary quotient of two given physical quantities.
    div(  input: PhysicalQuantityInput!, divisor: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int):PhysicalQuantity!
    # Compute the n-ary product of a given list of physical quantities.
    product( factors: [PhysicalQuantityInput]!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!

    # Compute the power of a given physical quantity to the specified integer exponent.
    power( input: PhysicalQuantityInput!, exponent: Int): PhysicalQuantity!

    # Compute the maximum of a given list of physical quantities.   Must be dimensionally consistent.
    maximum( inputs: [PhysicalQuantityInput]!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!
    # Compute the minimum of a given list of physical quantities.   Must be dimensionally consistent.
    minimum( inputs: [PhysicalQuantityInput]!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!
    # Compute the average of a given list of physical quantities.   Must be dimensionally consistent.
    average( inputs: [PhysicalQuantityInput]!, toUnitRef: UnitOfMeasureRef, precision: Int): PhysicalQuantity!

    # Test if a given physical quantity is greater than another given physical quantity.
    isGT( input: PhysicalQuantityInput!, rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if a given physical quantity is greater than or equal to another given physical quantity.
    isGTE( input: PhysicalQuantityInput!, rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if a given physical quantity is less than another given physical quantity.
    isLT( input: PhysicalQuantityInput!, rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if a given physical quantity is less than or equal to another given physical quantity.
    isLTE( input: PhysicalQuantityInput!, rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
    # Test if a given physical quantity is equal to another given physical quantity.
    isEqual( input: PhysicalQuantityInput!, rhs: PhysicalQuantityInput!, toUnitRef: UnitOfMeasureRef, precision: Int): Boolean!
 }

schema { query: Query }
```

## Examples

### Physical Quantities

| Example | How do I get the normalized surface form of a physical quantity? |
| :--- | :--- |
| Info | The physical quantity service automatically normalizes the surface form of physical quantities. Use the name property of the physical quantity type to get the normalized surface form of a physical quantity. The example below demonstrates how to get the normalized surface form of a physical quantity parsed from text. |
| Query | query { parse\(source:"ten psi"\){ name }} |
| Response | { "data": { "parse": { "name": "10.0 lb/in²" }}} |

| Example | How do I get the magnitude \(numeric value\) of a physical quantity? |
| :--- | :--- |
| Info | Use the magnitude property of the physical quantity type to get the numeric value of a physical quantity. The example below demonstrates how to get the numeric value of a physical quantity parsed from text. |
| Query | query { parse\(source:"ten psi"\){ magnitude }} |
| Response | { "data": { "parse": { "magnitude": 10 }}} |

| Example | How do I get the unit of measure of a physical quantity? |
| :--- | :--- |
| Info | Use the unitOfMeasure property of the physical quantity type to get the unit of measure of a physical quantity. The example below demonstrates how to get the unit of measure of a physical quantity parsed from text. |
| Query | query { parse\(source:"ten psi"\){ unitOfMeasure{ name }}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "name": "lb/in²" }}}} |

### Unit Of Measure

| Example | How can I see all the available units of measure? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL available units of measure. Instead, we can use the unitsOfMeasure query to find all the units of measure that have been explicitly defined. The physical quantity service comes out of the box with explicit definitions for the commonly used units of measure for both the MKS and Imperial systems of units. |
| Query | { query { unitsOfMeasure { name }}} |
| Result | { "data": { "unitsOfMeasure": \[{"name": "C"}, {"name": "m"}, { "name": "km"}, { "name": "D" }, .. AND MANY MORE \] }} |

| Example | How can I see all the available units of measure for a particular physical dimension? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL available units of measure. Instead, we can use the optional dimensionRef argument for the unitsOfMeasure query to find all the units of measure that have been explicitly defined for the given physical dimension. If no dimensionRef is provided, then all the explicitly defined units of measure are returned. The example below finds all the explicitly defined units of measure having a physical dimension of "LENGTH". |
| Query | { unitsOfMeasure\(dimensionRef: {name: "LENGTH"}\) { name }} |
| Response | { "data": { "unitsOfMeasure": \[ { "name": "m"}, { "name": "km"}, { "name": "ft"}, { "name": "in"}, { "name": "mi"}, { "name": "yd"}\]}} |

| Example | How do I get the normalized surface form of a unit of measure? |
| :--- | :--- |
| Info | The physical quantity service automatically normalizes the surface form of units of measure. Use the name property of the UnitOfMeasure type to get the normalized surface form. The example below demonstrates how to get the normalized surface form of a unit of measure from a physical quantity parsed from text. |
| Query | { parse\(source: "9.8 kilogram \* meter / second squared"\) {  unitOfMeasure {  name}}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "name": "kg·m/s²"}}}} |

| Example | How do I get the aliases for a unit of measure? |
| :--- | :--- |
| Info | Use the aliases property of the UnitOfMeasure type to get the aliases for the unit of measure. Any of the returned aliases could be used interchangeably with the preferred denotation. The example below demonstrates how to get the aliases for a unit of measure from a physical quantity parsed from text. |
| Query | { parse\(source: "55 mi/hr"\) {  unitOfMeasure {  aliases}}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "aliases": \[ "mph"\],}}}} |

| Example | How do I get the physical dimension of a unit of measure? |
| :--- | :--- |
| Info | Use the dimension property of the UnitOfMeasure type to get its physical dimension. The example below demonstrates how to get the physical dimension form of a unit of measure from a physical quantity parsed from text. |
| Query | { parse\(source: "9.8 kilogram \* meter / second squared"\) {  unitOfMeasure {  name, dimension {  name}}}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "dimension": { "name": "FORCE"}}}}} |

### Unit Of Measure Prefix

| Example | How can I see all the available unit of measure prefixes? |
| :--- | :--- |
| Info | Use the unitOfMeasurePrefixes query to find all the unit of measure prefixes. The physical quantity service comes out of the box with the metric system prefixes. |
| Query | { unitOfMeasurePrefixes{  name}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}} |

| Example | How do I get the preferred symbol for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the name property of the UnitOfMeasurePrefix to get the preferred symbol for the prefix. The example below gets the preferred symbol for all the unit of measure prefixes in the system: |
| Query | { unitOfMeasurePrefixes{  name}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}} |

| Example | How do I get the aliases for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the aliases property of the UnitOfMeasurePrefix to get all the aliases for the unit of measure prefix. The example below gets the aliases for all the unit of measure prefixes in the system: |
| Query | { unitOfMeasurePrefixes{  aliases}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "aliases": \[ "f", "fempto"\]}, { "aliases": \[ "p", "pico"\]}, { "aliases": \[ "n", "nano"\]}, { "aliases": \[ "μ", "u", "micro"\]}, ... AND MANY MORE\]}} |

| Example | How do I get the scaling factor for a unit of measure prefix? |
| :--- | :--- |
| Info | Use the multiplier property of the UnitOfMeasurePrefix to get all the scaling factor for the unit of measure prefix. The example below gets the multipliers for all the unit of measure prefixes in the system: |
| Query | { unitOfMeasurePrefixes{  multiplier}} |
| Response | { "data": { "unitOfMeasurePrefixes": \[ { "multiplier": 1e-15}, { "multiplier": 1e-12}, { "multiplier": 1e-9}, { "multiplier": 0.000001},  ... AND MANY MORE\]}} |

### Physical Dimensions

| Example | How can I see all the available physical dimensions? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of physical dimensions using multiplication, division and exponentiation, It is NOT possible to see ALL available physical dimensions. Instead, we can use the physicalDimensions query to find all the physical dimensions that have been explicitly defined. The physical quantity service comes out of the box with over 60 of the most commonly occurring physical dimensions in engineering and scientific literature. |
| Query | { query { physicalDimensions {  name}}} |
| Response | { "data": { "physicalDimensions": \[ { "name": "VELOCITY", "aliases": \[\], "formula": "L¹T⁻¹"}, { "name": "ELECTRICAL\_RESISTANCE", "aliases": \[\], "formula": "L²M¹T⁻³J⁻²"}, { "name": "ABASEMENT", "aliases": \[\], "formula": "L¹T¹"}, { "name": "MOMENTUM", "aliases": \[\], "formula": "L¹M¹T⁻¹" ... AND MANY  MORE},\]}} |

| Example | How do I look up a physicalDimension by its name, formula or alias? |
| :--- | :--- |
| Info | Use the physicalDimension query to search for a physical dimension based on its name, formula or alias. The example below finds a physical dimension by name: |
| Query | { physicalDimension\(dimensionRef:{name:"ELECTRICAL\_CURRENT"}\){  formula}} |
| Response | { "data": { "physicalDimension": { "formula": "J¹"}}} |

| Example | How do I get the preferred name of a physical dimension? |
| :--- | :--- |
| Info | Use the name field of the PhysicalDimension type to get the preferred name for a physical dimension. The example below gets the preferred name of all the explicitly defined physical dimensions: |
| Query | { query { physicalDimension\(dimensionRef: {  name}}} |
| Response | { "data": { "physicalDimensions": \[ { "name": "VELOCITY"}, { "name": "ELECTRICAL\_RESISTANCE"}, { "name": "ABASEMENT"}, { "name": "MOMENTUM"}, ... AND MANY MORE\]}} |

| Example | How can I get the aliases for a physical dimension? |
| :--- | :--- |
| Info | Use the name field of the PhysicalDimension type to get the preferred name for a physical dimension. The example below gets the aliases of all the explicitly defined physical dimensions having the formula "L²M¹T⁻²": |
| Query | { physicalDimension\(dimensionRef:{formula:"L²M¹T⁻²"}\){  aliases}} |
| Response | { "data": { "physicalDimension": { "aliases": \[ "ENERGY" "WORK", "TORQUE"\],}}} |

| Example | How can I get the formula for a physical dimension? |
| :--- | :--- |
| Info | Use the name formula of the PhysicalDimension type to get the formula for a physical dimension. The example below gets the formula of the explicitly defined physical dimension of "MOLAR\_HEAT\_CAPACITY": |
| Query | { physicalDimension\(dimensionRef:{name:"MOLAR\_HEAT\_CAPACITY"}\){  formula}} |
| Response | { "data": { "physicalDimension": { "formula": "L²M¹T⁻²ϴ⁻¹N⁻¹"}}} |

### Systems Of Units

| Example | How can I get a list of all the systems of units? |
| :--- | :--- |
| Info | Use the systemsOfUnits query to get a list of all the systems of units. For example the query below gets a list of all the names and descriptions of all the systems of units. |
| Query | query {  systemsOfUnits{  name, description}} |
| Response | { "data": { "systemsOfUnits": \[ { "name": "mks", "description": "mks \(meter-kilogram-second\) system"}, { "name": "Imperial", "description": "Imperial \(American\) Unit System"}\]}} |

| Example | How can I get a list of all the systems of units that contain a specific unit of measure? |
| :--- | :--- |
| Info | Use the systemsOfUnits field of the UnitOfMeasure type to get all the systems of units that contain that unit of measure. For example, the query below finds the unit of measure "second" and returns the names of all the systems of units in which it occurs: |
| Query | { unitOfMeasure\(unitRef:{alias:"second"}\){  systemsOfUnits{  name}}} |
| Response | { "data": { "unitOfMeasure": { "systemsOfUnits": \[ { "name": "mks"}, { "name": "Imperial"}\]}}} |

| Example | How can I get a list of all the systems of units that contain a specific unit of measure prefix? |
| :--- | :--- |
| Info | Use the systemsOfUnits field of the UnitOfMeasurePrefix type to get all the systems of units that contain that unit of measure prefix. For example, the query below finds the unit of measure prefix "kilo" and returns the names of all the systems of units in which it occurs: |
| Query | { unitOfMeasurePrefix\(prefixRef:{alias:"kilo"}\){  systemsOfUnits{  name}}} |
| Response | { "data": { "unitOfMeasurePrefix": { "systemsOfUnits": \[ { "name": "mks"}\]}}} |

| Example | How can I see all the units of measure in a system of units? |
| :--- | :--- |
| Info | Since the system can derive an infinite number of units of measure using multiplication, division and exponentiation, It is NOT possible to see ALL the units of measure in a system of units. Instead, you can use the units field of the SystemOfUnits type to get the list of explicitly defined units of measure that occur in that system of units. The optional "dimensionRef" parameter can be used to filter the results to those that have a specific physical dimension. The example below demonstrates how to find all the units of measure in the Imperial unit system that have dimension of LENGTH. |
| Query | query {  systemOfUnits\(systemRef:{name:"Imperial"}\){  units\(dimensionRef:{name:"LENGTH"}\){  name}}} |
| Response | { "data": { "systemOfUnits": { "units": \[ { "name": "ft"}, { "name": "in"}, { "name": "mi"}, { "name": "yd"}\]}}} |

| Example | How can I see all the unit of measure prefixes in a system of units? |
| :--- | :--- |
| Info | Use the prefixes field of the SystemOfUnits type to get the list of unit of measure prefixes in that system of units. The example below demonstrates how to find all the unit of measure prefixes in the mks unit system: |
| Query | query {  systemOfUnits\(systemRef:{name:"mks"}\){  prefixes{  name}}} |
| Response | { "data": { "systemOfUnits": { "prefixes": \[ { "name": "f"}, { "name": "p"}, { "name": "n"}, { "name": "μ"}, ... AND MANY MORE\]}}} |

### Parsing

| Example | How do I convert that string to a physical quantity? |
| :--- | :--- |
| Info | Use the parse query to convert a string into a physical quantity. The parse query can be used when you have a single string that is a surface form of an entity mention -- the parse query will throw an error if the provided string is not a surface form for some physical quantity. |
| Query | query { parse\(source:"400 psi"\){  name}} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

| Example | How do I convert the string to a physical quantity only if its unit of measure is in a particular system of units? |
| :--- | :--- |
| Info | Use the optional systemRefs parameter to specify either the id or name of the required systems of units. If no systemRefs parameter is provided, the parser uses all available systemsOfUnits. |
| Query | query { parse\(source:"400 psi",systemRefs:\[{name:"Imperial"}\]\){  name}} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

| Example | How do I convert the string to a physical quantity only if it has a specific physical dimension? |
| :--- | :--- |
| Info | Use the optional dimensionRef parameter to specify either the id, name or formula of the required physical dimension. If no dimensionRef parameter is provided, the parser admits all physical dimensions. |
| Query | query { parse\(source:"400 psi",dimensionRef:{name:"PRESSURE"}\){  name}} |
| Response | { "data": { "parse": { "name": "400.0 lb/in²"}}} |

### Extraction

| Example | You have a list of strings, each which may contain zero or many physical quantities. How do you extract all the physical quantities from the source text? |
| :--- | :--- |
| Info | Use the extract query to get all the entity mentions from the text. The extract method can be used to find entity mentions in a list of string literals, or the field of a kind. The extractor will find all the physical quantity mentions in each string \(or instance of a field\) and return a list of the results. |
| Query | { extract\(sources: \[ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."\]\) {  entity {  name}}} |
| Result | { "data": { "extract": \[ { "entity": { "name": "350.0 mi/hr"}}, { "entity": { "name": "3000 m"}}, { "entity": { "name": "7500.0 ft"}}\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. How do extract all the physical quantities from the source text only if their unit of measure is in the Imperial unit system? |
| :--- | :--- |
| Info | Use the optional systemRefs parameter to specify either the id or name of the required systems of units. If no systemRefs parameter is provided, the extractor uses all available systemsOfUnits. |
| Query | { extract\(sources: \[ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."\], systemRefs:\[{name:"Imperial"}\]\) {  entity {  name}}} |
| Response | { "data": { "extract": \[ { "entity": { "name": "350.0 mi/hr"}}, { "entity": { "name": "7500.0 ft"}}\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. How do extract all the physical quantities from the source text only if they have dimensions of length? |
| :--- | :--- |
| Info | Use the optional dimensionRef parameter to specify either the id, name or formula of the required physical dimension. If no dimensionRef parameter is provided, the extractor admits all physical dimensions. |
| Query | { extract\(sources: \[ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."\], dimensionRef:{name:"LENGTH"}\) {  entity {  name}}} |
| Response | { "data": { "extract": \[ { "entity": { "name": "3000.0 m"}}, { "entity": { "name": "7500.0 ft"}}\]}} |

| Example | You have a list of strings, each which may contain zero or more entity mentions. After extracting the entity mentions, how do you know where they occur in the source text? |
| :--- | :--- |
| Info | The fromKind, fromField, fromInstance, and fromString fields of the EntityMention type provide locators to the source text. The fromOffset field of the entity EntityMention type provides the distance from the start of the string \(in characters\) to the start of the entity mention. The fromSpan field of the EntityMention type provides the length of the entity mention \(in characters\). |
| Query | { extract\(sources: \[ "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "The minimum runway length is 7500 feet."\], dimensionRef:{name:"LENGTH"}\) {  entity {  name} fromText, fromSpan, fromOffset}} |
| Response | { "data": { "extract": \[ { "entity": { "name": "3000.0 m"}, "fromText": "the airspeed of the plane was 350 mph as it passed through 3000 meters.", "fromSpan": "12", "fromOffset": "59"}, { "entity": { "name": "7500.0 ft"}, "fromText": "The minimum runway length is 7500 feet.", "fromSpan": "10", "fromOffset": "29"}\]}} |

## Conversions

| Example | How do I convert a physical quantity to a different unit of measure? |
| :--- | :--- |
| Info | You can use the "convertTo" query, "convertTo" field, or "convertTo" argument to convert a physical quantity to a specific unit of measure. The examples below show examples of how to use each method to convert a pressure from atmospheres to gauge pressure. |
| using roundTo field | query {  convertTo\(input: {parseFrom: "2 atm"}, toUnitRef: {alias: "psi"}\) {  add\( summand: {parseFrom: "-14.69 psi"}\) {  roundTo\(precision: 3\) {  name}}}} |
| using convertTo field | query { parse\(source: "2 atm"\) {  add\(summand: {parseFrom: "-14.69 psi"}\) {  convertTo\(toUnitRef: {alias: "psi"}\) {  roundTo\(precision: 3\) {  name}}}}} |
| using arguments | query { add\(input:{parseFrom:"2 atm"},summand:{parseFrom:"-14.69 psi"},toUnitRef:{alias:"psi"},precision:3\){  name}} |
| Response | { "data": { "convertTo": { "add": { "roundTo": { "name": "14.7 lb/in²"}}}}} |

| Example | How can I tell if a physical quantity can be converted to a specific unit of measure? |
| :--- | :--- |
| Info | Use the canBeConverted to field of the UnitOfMeasure type to test if two units are interconvertible. The following query demonstrates how to test if a physical quantity can be converted to units of "psi' |
| Query | query{  parse\(source:"1 bar"\){  unitOfMeasure{  canBeConvertedTo\(toUnitRef:{name:"psi"}\)}}} |
| Response | { "data": { "parse": { "unitOfMeasure": { "canBeConvertedTo": true}}}} |

### Arithmetic

| Example | Add physical quantities with different dimensions |
| :--- | :--- |
| Info | The physical quantity service will perform the necessary unit conversions for you, so long as the units are dimensionally consistent |
| Query | { sum\(summands: \[{parseFrom: "12 inch"}, {parseFrom: "1 foot"}, {parseFrom: "1/3 yard"}\], precision: 2\) {  name}} |
| Response | { "data": { "sum": { "name": "36.0 in"}}} |

| Example | Multiplication and division |
| :--- | :--- |
| Info | When you pass arguments that have different units or physical dimensions, computer algebra on units of measure and physical dimensions automatically constructs the correct unit of measure for the returned physical quantity |
| Query | { product\(factors: \[{parseFrom: "12 inch"}, {parseFrom: "1 Hz"}, {parseFrom: "1/3 acre"}\], precision: 2\) {  name,  unitOfMeasure {  dimension {  name}}}} |
| Response | { "data": { "product": { "name": "4.0 Hz·acre·in", "unitOfMeasure": { "dimension": { "name": "VOLUMETRIC\_FLOW"}}}}} |

### Size Comparisons

| Example | Comparing size without converting units |
| :--- | :--- |
| Info | The physical quantity service will perform the necessary unit conversions for you, as long as the units are dimensionally consistent |
| Query | { extract\(sources: \["1 inch","1 foot","1 yard"\]\) {  entity{  isGT\(rhs:{parseFrom:"2 1/2 feet"}\), isGTE\(rhs:{parseFrom:"2.54 centimeters"}\), isEqual\(rhs:{parseFrom:"3 feet"}\), isLT\(rhs:{parseFrom:"0.5 meters"}\), isLTE\(rhs:{parseFrom:"1/5280 mile"}\)}}} |
| Response | { "data": { "extract": \[ { "entity": { "isGT": false, "isGTE": true, "isEqual": false, "isLT": true, "isLTE": true}}, { "entity": { "isGT": false, "isGTE": true, "isEqual": false, "isLT": true, "isLTE": true}}, { "entity": { "isGT": true, "isGTE": true, "isEqual": true, "isLT": false, "isLTE": false}}\]}} |

