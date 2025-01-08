# Test-Driven Development (TDD) Guide

## ⚠️ PRIORITY 1: TDD WORKFLOW IS MANDATORY ⚠️

All development MUST follow Test-Driven Development principles. No code should be written without first writing a failing test.

## Overview
This guide demonstrates the mandatory Test-Driven Development workflow for the WP Admin Dashboard Optimizer Plugin. The "Red, Green, Refactor" cycle must be followed with small, iterative steps.

## TDD Principles (Required)

1. Write a failing test first (Red)
   - No exceptions - every feature starts with a test
   - Test must fail initially to prove it works
   - Document test purpose clearly

2. Write minimal code to make the test pass (Green)
   - Write only enough code to pass the test
   - No additional functionality
   - No premature optimization

3. Clean up the code while keeping tests passing (Refactor)
   - Improve code quality
   - Maintain test coverage
   - Document changes

4. Repeat for next small piece of functionality
   - Keep iterations small
   - Commit after each cycle
   - Maintain test documentation

## Example TDD Workflow

### Cycle 1: Validate Card Number Format

#### Step 1: Write Failing Test (Red)
```php
class GiftCardValidatorTest extends WP_UnitTestCase {
    public function test_card_number_must_be_16_digits() {
        $validator = new GiftCardValidator();
        $this->assertFalse($validator->isValidCardNumber('123')); // Too short
        $this->assertFalse($validator->isValidCardNumber('12345678901234567')); // Too long
        $this->assertTrue($validator->isValidCardNumber('1234567890123456')); // Just right
    }
}
```

Run test (it fails because class doesn't exist):
```bash
./vendor/bin/phpunit --filter=test_card_number_must_be_16_digits
```

#### Step 2: Write Minimal Code (Green)
```php
class GiftCardValidator {
    public function isValidCardNumber($number) {
        return strlen($number) === 16;
    }
}
```

Run test (it passes):
```bash
./vendor/bin/phpunit --filter=test_card_number_must_be_16_digits
```

#### Step 3: Refactor
No refactoring needed yet - code is simple and clear.

### Cycle 2: Add Luhn Algorithm Check

#### Step 1: Write Failing Test (Red)
```php
public function test_card_number_passes_luhn_check() {
    $validator = new GiftCardValidator();
    $this->assertTrue($validator->isValidCardNumber('4532015112830366')); // Valid number
    $this->assertFalse($validator->isValidCardNumber('4532015112830367')); // Invalid check digit
}
```

Run test (it fails because Luhn check isn't implemented):
```bash
./vendor/bin/phpunit --filter=test_card_number_passes_luhn_check
```

#### Step 2: Write Minimal Code (Green)
```php
class GiftCardValidator {
    public function isValidCardNumber($number) {
        if (strlen($number) !== 16) {
            return false;
        }
        
        return $this->passesLuhnCheck($number);
    }
    
    private function passesLuhnCheck($number) {
        $sum = 0;
        $length = strlen($number);
        
        for ($i = 0; $i < $length; $i++) {
            $digit = (int) $number[$length - 1 - $i];
            if ($i % 2 === 1) {
                $digit *= 2;
                if ($digit > 9) {
                    $digit -= 9;
                }
            }
            $sum += $digit;
        }
        
        return $sum % 10 === 0;
    }
}
```

Run test (it passes):
```bash
./vendor/bin/phpunit --filter=test_card_number_passes_luhn_check
```

#### Step 3: Refactor
Extract Luhn check to separate method for clarity:
```php
class GiftCardValidator {
    public function isValidCardNumber($number) {
        return strlen($number) === 16 && $this->passesLuhnCheck($number);
    }
    
    private function passesLuhnCheck($number) {
        return $this->calculateLuhnSum($number) % 10 === 0;
    }
    
    private function calculateLuhnSum($number) {
        $sum = 0;
        $length = strlen($number);
        
        for ($i = 0; $i < $length; $i++) {
            $digit = (int) $number[$length - 1 - $i];
            if ($i % 2 === 1) {
                $digit = $this->doubleDigit($digit);
            }
            $sum += $digit;
        }
        
        return $sum;
    }
    
    private function doubleDigit($digit) {
        $doubled = $digit * 2;
        return $doubled > 9 ? $doubled - 9 : $doubled;
    }
}
```

Run all tests (they still pass):
```bash
./vendor/bin/phpunit --filter=GiftCardValidatorTest
```

### Cycle 3: Add Card Type Validation

#### Step 1: Write Failing Test (Red)
```php
public function test_identifies_card_type() {
    $validator = new GiftCardValidator();
    $this->assertEquals('visa', $validator->getCardType('4532015112830366'));
    $this->assertEquals('mastercard', $validator->getCardType('5332015112830366'));
    $this->assertEquals('amex', $validator->getCardType('373201511283036'));
    $this->assertEquals('discover', $validator->getCardType('6011015112830366'));
}
```

Run test (it fails because method doesn't exist):
```bash
./vendor/bin/phpunit --filter=test_identifies_card_type
```

#### Step 2: Write Minimal Code (Green)
```php
public function getCardType($number) {
    if (substr($number, 0, 1) === '4') return 'visa';
    if (substr($number, 0, 1) === '5') return 'mastercard';
    if (substr($number, 0, 1) === '3') return 'amex';
    if (substr($number, 0, 1) === '6') return 'discover';
    return null;
}
```

Run test (it passes):
```bash
./vendor/bin/phpunit --filter=test_identifies_card_type
```

#### Step 3: Refactor
Make card type rules more explicit and maintainable:
```php
class GiftCardValidator {
    private const CARD_PATTERNS = [
        'visa' => '/^4[0-9]{12}(?:[0-9]{3})?$/',
        'mastercard' => '/^5[1-5][0-9]{14}$/',
        'amex' => '/^3[47][0-9]{13}$/',
        'discover' => '/^6(?:011|5[0-9]{2})[0-9]{12}$/'
    ];
    
    public function getCardType($number) {
        foreach (self::CARD_PATTERNS as $type => $pattern) {
            if (preg_match($pattern, $number)) {
                return $type;
            }
        }
        return null;
    }
}
```

Run all tests (they still pass):
```bash
./vendor/bin/phpunit --filter=GiftCardValidatorTest
```

## Test Structure

```
tests/
├── bootstrap.php           # Test initialization
├── unit/                  # Unit tests
│   ├── Core/             
│   │   └── GiftCardValidatorTest.php
│   ├── Security/         
│   ├── Database/         
│   └── API/              
├── integration/           # Integration tests
└── security/             # Security-specific tests
```

## Running Tests

```bash
# Run all tests
composer test

# Run specific test
./vendor/bin/phpunit --filter=test_name

# Run with coverage
composer test-coverage
```

## TDD Best Practices

1. **Keep Tests Focused**
   - One assertion per test when possible
   - Test one piece of functionality at a time
   - Use descriptive test names

2. **Red-Green-Refactor Cycle**
   - Write failing test first (Red)
   - Write minimal code to pass (Green)
   - Clean up code while keeping tests passing (Refactor)
   - Repeat for next small feature

3. **Small Steps**
   - Add one small test at a time
   - Write minimal code to pass each test
   - Refactor after each green test
   - Build complexity gradually

4. **Maintain Test Quality**
   - Keep tests simple and readable
   - Don't test implementation details
   - Test behavior, not methods
   - Use meaningful assertions

This TDD workflow ensures:
1. Code is thoroughly tested
2. Design emerges from requirements
3. Complexity is managed
4. Changes are safe to make
