# Wrangler Enhancement: Byte Size and Time Duration Units Parsers

Author: Aastha Gupta

## Overview
This project enhances the CDAP Wrangler library with native support for parsing and utilizing byte size and time duration units. This makes it easier for users to work with data that includes units like Kilobytes (KB), Megabytes (MB), milliseconds (ms), or seconds (s), eliminating the need for complex multi-step recipes.

## Features Added

### 1. Byte Size Parser
The Byte Size parser recognizes values with the following units:
- B (bytes)
- KB (kilobytes)
- MB (megabytes)
- GB (gigabytes)
- TB (terabytes)

Example usage:
```
set-column :size 1.5MB
```

### 2. Time Duration Parser
The Time Duration parser recognizes values with the following units:
- ms (milliseconds)
- s (seconds)
- m (minutes)
- h (hours)

Example usage:
```
set-column :duration 500ms
```

### 3. Aggregate Stats Directive
The new `aggregate-stats` directive aggregates byte size and time duration values with optional unit conversion.

Syntax:
```
aggregate-stats <byte_size_column> <time_duration_column> <total_size_column> <total_time_column> [<output_size_unit> <output_time_unit>]
```

Parameters:
- `byte_size_column`: Column containing byte size values
- `time_duration_column`: Column containing time duration values
- `total_size_column`: Target column name for total size
- `total_time_column`: Target column name for total time
- `output_size_unit` (optional): Output unit for size (B, KB, MB, GB, TB)
- `output_time_unit` (optional): Output unit for time (ns, ms, s, m, h)

Example:
```
aggregate-stats :data_transfer_size :response_time total_size_mb total_time_sec MB sec
```

## Implementation Details

1. **Grammar Modifications**
   - Added lexer rules for BYTE_SIZE and TIME_DURATION tokens
   - Updated parser rules to accept these new tokens
   - Used helper fragments (BYTE_UNIT, TIME_UNIT) to define the supported units

2. **API Updates**
   - Created ByteSize and TimeDuration classes that extend Token
   - Added methods to convert between different units
   - Added new token types to TokenType enum

3. **Core Parser Updates**
   - Enhanced the parser to handle the new token types
   - Updated the visitor methods to process byte size and time duration values

4. **New Directive**
   - Implemented aggregate-stats directive for aggregating statistics on byte sizes and time durations
   - Added support for unit conversion
   - Utilized ExecutorContext store for maintaining aggregation state

## Usage Examples

### Basic Aggregation
```
aggregate-stats :data_size :response_time total_size total_time
```
This aggregates the data sizes and response times into their respective total columns using default units.

### Aggregation with Unit Conversion
```
aggregate-stats :data_size :response_time total_size_gb total_time_min GB m
```
This aggregates the data sizes and response times, converting the totals to gigabytes and minutes respectively.

## Testing

Comprehensive test cases have been implemented to ensure the correctness and robustness of the new features:

- Unit tests for ByteSize and TimeDuration classes
- Parser tests to validate correct syntax parsing
- Integration tests for the aggregate-stats directive with various input scenarios
- Edge case handling (zero values, large numbers, different unit cases)

## Building and Running

1. Clone the repository:
```
git clone https://github.com/aastha-gupta/wrangler.git
```

2. Build the project:
```
cd wrangler
mvn clean install
```

3. Run the tests:
```
mvn test
```

## Dependencies

- Java 8 or higher
- Maven 3.6 or higher
- ANTLR 4
