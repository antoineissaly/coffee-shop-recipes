# Testing Practices Cheat Sheet

## Testing Libraries and Frameworks

- Jest: Primary testing framework
- Mockito: Used for mocking in Java tests
- PowerMock: Extended mocking capabilities for static methods and constructors

## Mocking and Stubbing Strategies

### Jest Mocks

```javascript
jest.mock('../path/to/module');
const mockFunction = jest.fn();
```

### Mockito Mocks

```java
@Mock
private DependencyClass mockDependency;

when(mockDependency.someMethod()).thenReturn(expectedValue);
```

### PowerMock for Static Methods

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({StaticClass.class})
public class TestClass {
    @Test
    public void testStaticMethod() {
        PowerMockito.mockStatic(StaticClass.class);
        when(StaticClass.staticMethod()).thenReturn(expectedValue);
    }
}
```

## Fake Implementations

### In-Memory Repositories

```java
public class InMemoryUserRepository implements UserRepository {
    private Map<String, User> users = new HashMap<>();

    @Override
    public User findById(String id) {
        return users.get(id);
    }

    @Override
    public void save(User user) {
        users.put(user.getId(), user);
    }
}
```

## Test Structure

### Arrange-Act-Assert (AAA) Pattern

```javascript
test('should calculate total correctly', () => {
    // Arrange
    const cart = new ShoppingCart();
    cart.addItem({ price: 10 });
    cart.addItem({ price: 20 });

    // Act
    const total = cart.calculateTotal();

    // Assert
    expect(total).toBe(30);
});
```

## Test Naming Conventions

- Use descriptive names: `test_should_return_correct_total_when_items_added()`
- Follow a consistent naming pattern: `test_[UnitOfWork]_[StateUnderTest]_[ExpectedBehavior]()`

## Test Coverage

- Aim for high test coverage, especially for critical paths
- Use Jest's coverage reports to identify untested areas

```javascript
jest --coverage
```

## Parameterized Tests

### JUnit Parameterized Tests

```java
@RunWith(Parameterized.class)
public class CalculatorTest {
    @Parameters
    public static Collection<Object[]> data() {
        return Arrays.asList(new Object[][] {
            { 1, 1, 2 },
            { 2, 2, 4 },
            { 3, 3, 6 }
        });
    }

    private int input1;
    private int input2;
    private int expected;

    public CalculatorTest(int input1, int input2, int expected) {
        this.input1 = input1;
        this.input2 = input2;
        this.expected = expected;
    }

    @Test
    public void testAdd() {
        Calculator calc = new Calculator();
        assertEquals(expected, calc.add(input1, input2));
    }
}
```

## Error Handling Tests

- Test both success and failure scenarios
- Verify correct error messages and types are thrown

```javascript
test('should throw error for invalid input', () => {
    expect(() => {
        validateInput('');
    }).toThrow('Input cannot be empty');
});
```

## Integration Tests

- Use real dependencies where possible
- Mock external services and APIs

```javascript
describe('UserService Integration', () => {
    let userService;
    let mockDatabase;

    beforeEach(() => {
        mockDatabase = new MockDatabase();
        userService = new UserService(mockDatabase);
    });

    test('should create user and store in database', async () => {
        const user = { name: 'John Doe', email: 'john@example.com' };
        await userService.createUser(user);
        const storedUser = await mockDatabase.findUserByEmail('john@example.com');
        expect(storedUser).toEqual(user);
    });
});
```

## Asynchronous Testing

### Jest Async Tests

```javascript
test('async operation completes successfully', async () => {
    const result = await asyncOperation();
    expect(result).toBe('success');
});
```

### Java CompletableFuture Tests

```java
@Test
public void testAsyncOperation() throws Exception {
    CompletableFuture<String> future = asyncService.performAsyncOperation();
    String result = future.get(5, TimeUnit.SECONDS);
    assertEquals("expected result", result);
}
```

## Test Data Management

- Use factories or builders for creating test data
- Separate test data creation from test logic

```javascript
const createTestUser = (overrides = {}) => ({
    id: 'user123',
    name: 'Test User',
    email: 'test@example.com',
    ...overrides
});

test('user creation', () => {
    const user = createTestUser({ name: 'Custom Name' });
    expect(user.name).toBe('Custom Name');
});
```

## Mocking Time-Dependent Tests

### Jest Timers

```javascript
jest.useFakeTimers();

test('setTimeout callback', () => {
    const callback = jest.fn();
    setTimeout(callback, 1000);
    
    jest.runAllTimers();
    expect(callback).toHaveBeenCalled();
});
```

### Java Clock Mocking

```java
@Test
public void testTimeDependent() {
    Clock fixedClock = Clock.fixed(Instant.parse("2021-01-01T00:00:00Z"), ZoneId.systemDefault());
    TimeService timeService = new TimeService(fixedClock);
    
    assertEquals("2021-01-01", timeService.getCurrentDate());
}
```