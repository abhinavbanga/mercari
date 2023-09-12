# mercari

**Scenario 1: Implement a Positive End-to-End Test**

First, set up the basic structure for tests, including test classes and methods. In Jetpack Compose, use `@HiltAndroidTest` annotation to enable Hilt dependency injection in the tests.

@HiltAndroidTest
class PurchaseFlowTest {

    @Inject
    lateinit var userRepository: UserRepository // Assuming I have a UserRepository for authentication

    @get:Rule
    var hiltRule = HiltAndroidRule(this)

    @get:Rule
    var composeTestRule = createComposeRule()

    @Before
    fun setup() {
        // Perform any setup needed before the test
    }

    @Test
    fun testPurchaseFlow() {
        // Step 1: Login
        userRepository.login("username", "password")
        composeTestRule.onNodeWithText("Showcase Screen").assertIsDisplayed()

        // Step 2: Open the first item
       (In the code below, we assume that the first item in the Showcase Screen has the text or content description "Item 1."The performClick() method simulates a click action on the specified UI 
        element, which, in this case, is the first item.After clicking on the first item, we call waitForScreen("Item Screen") to wait for the Item Screen to appear, as discussed in Scenario 2 to ensure that 
        the transition to the new screen is complete before proceeding with the test.)

        composeTestRule.onNodeWithText("Item 1").performClick() // Replace "Item 1" with the actual text or content description of the first item
        waitForScreen("Item Screen") // Wait for the Item Screen to appear

        // Step 3: Add item to cart
      (In the code below, we assume that there's an "Add to Cart" button on the Item Screen, and after clicking it, a confirmation message with the text "Item Added to Cart" is displayed. The performClick() 
      method simulates a click action on the "Add to Cart" button, and assertIsDisplayed() verifies that the confirmation message appears on the screen to confirm that the item has been added to the cart.)
      
        composeTestRule.onNodeWithText("Add to Cart").performClick() // Replace "Add to Cart" with the actual text or content description of the add-to-cart button
        composeTestRule.onNodeWithText("Item Added to Cart").assertIsDisplayed() // Replace "Item Added to Cart" with the confirmation message

        // Step 4: Go back to Showcase Screen and add the second item
      (In the code below, first simulate clicking a "Back" button to return to the Showcase Screen. Replace "Back" with the actual text or content description of the button that navigates back.After returning 
       to the Showcase Screen, locate and click on the second item (in this, "Item 2") to navigate to the Item Screen for the second item. Replace "Item 2" with the actual text or content description of the 
       second item in the app. Then wait for the Item Screen to appear using the waitForScreen function, as discussed in Scenario 2. Finally, perform the same steps as in Step 3 to add the second item to the 
       cart and assert that the confirmation message is displayed.)

        composeTestRule.onNodeWithText("Back").performClick() // Replace "Back" with the actual text or content description of the back button
        waitForScreen("Showcase Screen") // Wait for the Showcase Screen to appear
        composeTestRule.onNodeWithText("Item 2").performClick() // Replace "Item 2" with the actual text or content description of the second item
        waitForScreen("Item Screen") // Wait for the Item Screen to appear
        composeTestRule.onNodeWithText("Add to Cart").performClick()
        composeTestRule.onNodeWithText("Item Added to Cart").assertIsDisplayed()


        // Step 5: Go to Cart Screen and start the purchase flow
      (In the code below,simulate clicking on a UI element with the text or content description "Cart" to navigate to the Cart Screen. Replace "Cart" with the actual text or content description of the cart 
       icon or button in the app. After navigating to the Cart Screen, perform further actions to start the purchase flow, such as clicking a "Checkout" button or any other relevant action 
       specific to the app's UI.Use the waitForScreen function to wait for the Cart Screen and subsequent screens (e.g., Address Screen) to appear, as discussed in Scenario 2.Continue with the rest of the 
       purchase flow as needed, interacting with UI elements to complete the purchase.)
  
        composeTestRule.onNodeWithText("Cart").performClick() // Replace "Cart" with the actual text or content description of the cart icon or button
        waitForScreen("Cart Screen") // Wait for the Cart Screen to appear
        composeTestRule.onNodeWithText("Checkout").performClick()
        waitForScreen("Address Screen") // Wait for the Address Screen to appear

        // Step 6: Complete the address and continue to Confirm Purchase Screen
      (In the code below, use performTextInput to input the required address details. Replace "Full Name," "Address," "City," and other field identifiers with the actual labels or hint texts used in the app for 
      the corresponding address fields. After entering the address details, simulate clicking a "Continue" button or any other relevant action to proceed to the Confirm Purchase Screen. Replace "Continue" with 
      the actual text or content description of the button or action that advances to the next screen.I used the waitForScreen function to ensure that the Confirm Purchase Screen is displayed before proceeding 
      with further interactions.)

        composeTestRule.onNodeWithText("Full Name").performTextInput("John Doe") // Replace "Full Name" with the actual label or hint text for the full name field
        composeTestRule.onNodeWithText("Address").performTextInput("123 Main St") // Replace "Address" with the actual label or hint text for the address field
        composeTestRule.onNodeWithText("City").performTextInput("Cityville") // Replace "City" with the actual label or hint text for the city field
        composeTestRule.onNodeWithText("Continue").performClick() // Replace "Continue" with the actual text or content description of the continue button
        waitForScreen("Confirm Purchase Screen") // Wait for the Confirm Purchase Screen to appear

        // Step 7: Finish the purchase and verify success
      (In the code below, simulate clicking a "Finish Purchase" button or performing any action that completes the purchase. Replace "Finish Purchase" with the actual text or content description of the button 
       or action in the app that completes the purchase.Use the waitForScreen function to wait for the Purchase Successful Screen to appear before proceeding with verification.To verify the success of the 
       purchase, use assertIsDisplayed() to check if a success message or element (in this case, "Purchase Successful") is displayed on the screen.)

        composeTestRule.onNodeWithText("Finish Purchase").performClick() // Replace "Finish Purchase" with the actual text or content description of the button that completes the purchase
        waitForScreen("Purchase Successful Screen") // Wait for the Purchase Successful Screen to appear
        composeTestRule.onNodeWithText("Purchase Successful").assertIsDisplayed() // Replace "Purchase Successful" with the actual success message or content on the screen

    }

    @After
    fun teardown() {
    // Perform cleanup tasks here, such as resetting database state, clearing shared preferences, or releasing resources.
    // 1. Reset database state
    database.clearTables()

    // 2. Clear shared preferences
    val sharedPreferences = context.getSharedPreferences("my_prefs", Context.MODE_PRIVATE)
    sharedPreferences.edit().clear().apply()

    // 3. Release any resources (e.g., close open files or connections)
    resourceManager.releaseResources()
    }
}

**Scenario 2: Handle Screen Transitions and Compose Test Setup**

1. To track screen transitions and handle network latency, create a custom function that waits for a certain condition to be met. Use `composeTestRule.onNode` and `composeTestRule.waitFor` to achieve this.
fun waitForScreen(screenName: String) {
    composeTestRule.waitForIdle()
    composeTestRule.onNodeWithText(screenName).assertIsDisplayed()
    Log.d("TestLog", "User is on $screenName")
}

2. Before the test starts, perform some setup tasks. This could include initializing the database, clearing shared preferences, or setting up mocks. One can do this in the `@Before` method in the test class.

3. Regarding the bonus point (5a.) in Scenario 1, asserting the quantity of items in the cart is a good practice. One can add an assertion like this:
val cartItems = composeTestRule.onAllNodesWithText("Item Name in Cart").toList()
assertEquals(2, cartItems.size) // Assuming 2 items were added to the cart

For design patterns, use the Page Object Model (POM) to encapsulate the interaction logic for each screen. This makes tests more maintainable and easier to read.

Lastly, don't forget to add the necessary dependencies and configurations in the app's `build.gradle` file to enable Jetpack Compose testing:

```gradle
android {
    buildFeatures {
        compose true
    }
}
dependencies {
    androidTestImplementation "androidx.compose.ui:ui-test-junit4:${compose_version}"
    androidTestImplementation "com.google.dagger:hilt-android-testing:${hilt_version}"
}
```
