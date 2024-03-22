
# Reflect Telegram Bot Library (Version 1.1.2) 🤖

Developing Telegram bots in Java is a breeze with `io.github.reflectframework:reflect-telegram-bot`! 🚀

## Abstraction of Telegram API Methods

This library provides a high-level abstraction over Telegram bot API methods. Say goodbye to tedious low-level details and focus on your bot's brilliance! 🌟

```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.utils.enums.KeyboardType;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Sender sender;

    @TextMapping(regexp = "/start")
    public UserState showStartMenu(HashedUser user) {
        sender.sendMessage(user, "📲 Enter Your Phone", KeyboardType.CONTACT);
        return State.SEND_PHONE;
    }
    
}
```

## Event Handling and Callback Abstractions

Handle incoming messages and events effortlessly! The library adopts an event-driven model and magical abstractions, making your bot a responsive chatterbox. 📬

```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.ContactMapping;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Sender sender;

    @ContactMapping(states = {State.Fields.SEND_PHONE}, target = ContactMappingTarget.PHONE_NUMBER)
    public UserState savePhoneAndShowMainMenu(HashedUser user, String phoneNumber) {
        // ... Saving phone number
        sender.sendMessage(user, "Choose one...", List.of(List.of(SEARCH_MODE,SETTINGS)));
        return State.MAIN_MENU;
    }
    
}
```

## Built-in Error Handling

Boost your bot's resilience with built-in error handling. No more stressing over API hiccups – let the library handle it like a pro! 💪

```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.RequiredArgsConstructor;

@BotController(order = Integer.MAX_VALUE)
@RequiredArgsConstructor
public class ExceptionController {
    
    private final Sender sender;

    @TextMapping(regexp = "[\\w.-]*")
    public UserState exceptionHandler(HashedUser user) {
        sender.sendMessage(user, "♨️ You entered unknown option, please try again!");
        return user.getState();
    }
    
}
```

## Clean Code Organization

Keep your codebase as sleek as your bot's interactions! The library encourages clean and organized code, making maintenance a walk in the digital park. 🏞️


```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.RequiredArgsConstructor;

@BotController(order = 1)
@RequiredArgsConstructor
public class SettingsController {
    
    private final Sender sender;

    @CallbackQueryMapping(dataRegexp = "(" + LANGUAGE_EN + "|" + LANGUAGE_RU + "+)", target = CallbackQueryMappingTarget.QUERY_DATA)
    public UserState changeLanguage(HashedUser user, String data)
          
          // Updating language...
          
        sender.editMessageText(user, "Choose one...", List.of(List.of(CHANGE_PHONE,CHANGE_LANGUAGE),List.of(BACK_TO_MAIN_MENU)));
        return State.SETTINGS_MENU;
    }
    
}
```

## Community Support

Join a vibrant community that breathes life into your bot! Get ready for a wave of documentation, examples, and community support. You're not alone on this coding adventure! 🌐

## Updates and Maintenance

Stay ahead of the Telegram API game! The library, like a diligent sidekick, undergoes regular updates, ensuring your bot effortlessly adapts to Telegram's evolving features. 🔧

Before you embark on this coding journey, dive into the library's documentation for insights, usage tips, and the secret sauce that makes it perfect for Telegram bot development! 📚

---

# ⚡**Quick Start**
The library `io.github.reflectframework:reflect-telegram-bot` is designed to specifically operate with the Telegram bot webhook method. A webhook is a mechanism where incoming messages and updates are sent directly to a specified URL in real-time.

# 🚀 Getting started with Reflect Telegram Bot
`Note❗️` Library is created based on spring boot starter dependencies. The minimum version of spring boot is 3.0.0 and the minimum version of java is 17. Please make sure you are using exactly same or higher versions!

First of all add dependency to your project with one of options below:

1. Using Maven Central Repository:
```xml
<dependency>
    <groupId>io.github.reflectframework</groupId>
    <artifactId>reflect-telegram-bot</artifactId>
    <version>1.1.2</version>
</dependency>
```
2. Using Gradle(Short): 
```gradle
implementation 'io.github.reflectframework:reflect-telegram-bot:1.1.2'
```
3. Using Gradle(Kotlin): 
```gradle
implementation("io.github.reflectframework:reflect-telegram-bot:1.1.2")
```
---

### **🏁 Step 1️⃣:** Setting required credentials via application.yml file
```yaml
bot:
  production-mode: false  # Default 'false', default uses telegram long polling method, if specified 'true', telegram webhook method will be autoconfigured
  domain: YOUR_DOMAIN  # https://example.com or example.com; Specify this property when 'production-mode' marked as 'true';
  token:  YOUR_BOT_TOKEN  # You can learn how to get it from https://core.telegram.org/bots/faq#how-do-i-create-a-bot;
  username: YOUR_BOT_USERNAME  # Specify this property when 'production-mode' marked as 'false'; 
```
In case, you want to check production mode locally, we highle recommend you to use [NGROK](https://ngrok.com/). This is a versatile and popular tool used for exposing local servers to the internet. You can download it from [here!](https://ngrok.com/download)

---

### **🏁 Step 2️⃣:** Creating type for User State as Enum
```java
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.experimental.FieldNameConstants;

@FieldNameConstants(onlyExplicitlyIncluded = true)
public enum State implements UserState {
    @FieldNameConstants.Include INIT,
    @FieldNameConstants.Include SEND_PHONE,
    @FieldNameConstants.Include MAIN_MENU
}
```
This is simple enum, but marked as UserState. This helps to manage users and their actions! As you can see, here used [Lomboks's](https://projectlombok.org/) [@FieldNameConstants](https://projectlombok.org/features/experimental/FieldNameConstants#:~:text=The%20%40FieldNameConstants%20annotation%20generates%20an,static%20final%20%2C%20of%20type%20java.) annotation. The purpose of using this annotation is to create identical constants with enums.

---

### **🏁 Step 3️⃣:** Creating type for User Language as Enum  `(Optional)`
```java
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserLanguage;

public enum Language implements UserLanguage {
    EN("en"),
    RU("ru");
    
    private final String code;
    
    Language(String code) {
        this.code = code;
    }
    @Override
    public String getCode() {
        return code;
    }
}
```
This is simple enum, but marked as UserLanguage. This helps you to work with multi languges. Here the `code` is suffix of one of the `messages.properties` file(ex: `messages_ru.properties`).The `code` must not be wrong! You can learn more about Spring's i18 configuration from [here](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-internationalization.html)

---
### **🏁 Step 4️⃣:** Marking User Entity as Telegram User Details
```java
import io.github.reflectframework.reflecttelegrambot.entities.user.TelegramUserDetails;
import jakarta.annotation.Nonnull;
import jakarta.annotation.Nullable;
import jakarta.persistence.*;

@Entity
public class UserEntity implements TelegramUserDetails {

    private String name;

    private long chatId;

    @Enumerated(EnumType.STRING)
    private State state;

    @Enumerated(EnumType.STRING)
    private Language language;

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    public UserEntity(String name, long chatId, State state, Language language) {
        this.name = name;
        this.chatId = chatId;
        this.state = state;
        this.language = language;
    }
    
     public UserEntity() {}

    @Nonnull
    @Override
    public Long getChatId() {
        return chatId;
    }

    @Nonnull
    @Override
    public UserState getState() {
        return state;
    }

    @Nullable
    @Override
    public UserLanguage getLanguage() {
        return language;
    }
}
```
`io.github.reflectframework.reflecttelegrambot.entities.user.TelegramUserDetails` is just interface and requires to implement three methods which `getChatId()`, `getState()` and `getLanguage()`. `getLanguage()` is optional and if you down't want to use it, you can just return `null` instead of creating `enum` and storing it to database!

---
### **🏁 Step 5️⃣:** Marking User Service as Telegram User Details Service
```java
import jakarta.annotation.Nonnull;
import jakarta.annotation.Nullable;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.telegram.telegrambots.meta.api.objects.User;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.entities.user.TelegramUserDetails;
import io.github.reflectframework.reflecttelegrambot.services.user.TelegramUserDetailsService;

@Service
@RequiredArgsConstructor
public class UserService implements TelegramUserDetailsService {

    private final UserRepository userRepository;

    @Nullable
    @Override
    public TelegramUserDetails findByChatId(long chatId) {
        return userRepository.findByChatId(chatId);
    }

    @Nonnull
    @Override
    public TelegramUserDetails save(@Nonnull User user, long chatId) {
        return userRepository.save(new UserEntity(user.getFirstName(), user.getUserName(), chatId, State.INIT, Language.EN));
    }

    @Override
    public void update(@Nonnull HashedUser user) {
        UserEntity entity = userRepository.findByChatId(user.getChatId());
        if (user.getState() instanceof State state && user.getLanguage() instanceof Language language) {
            entity.setState(state);
            entity.setLanguage(language);
            userRepository.save(entity);
        }
    }
}
```
`io.github.reflectframework.reflecttelegrambot.services.user.TelegramUserDetailsService` is interface that requires to implement three methods as described below:
 - `public TelegramUserDetails findByChatId(long chatId)` - this method need to search user by `chatId` and return. In case, the user is new `null` must be returned.
 - `public TelegramUserDetails save(@Nonnull User user, long chatId)` - this method need to save a new `org.telegram.telegrambots.meta.api.objects.User` to database. Only called when `public TelegramUserDetails findByChatId(long chatId)` returns `null`.
 - `public void update(@Nonnull HashedUser user)` - this method need to update stored user info when this method called. Changed data come as `io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser` 
 
---
### **🏁 Step 6️⃣:** Enable `io.github.reflectframework:reflect-telegram-bot` auto configurations
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import io.github.reflectframework.reflecttelegrambot.annotations.EnableBot;

@EnableBot
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(UnitBotApplication.class, args);
    }

}
```
Simply add the `@EnableBot` annotation to your bootstrap class and the necessary configurations will be generated for you!

---
### **🏁 Step 7️⃣:** Creating Bot Controller
_Now you can catch whatever you want with small piece of code, just feel free and work with your business logic!_
```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.CallbackQueryMapping;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.ContactMapping;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.LocationMapping;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.file.PhotoMapping;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import org.telegram.telegrambots.meta.api.objects.CallbackQuery;
import org.telegram.telegrambots.meta.api.objects.Location;
import org.telegram.telegrambots.meta.api.objects.PhotoSize;

@BotController
public class Controller {
    
    @TextMapping(regexp = "YOUR_REGULAR_EXPRESSION")
    public UserState textMapping(HashedUser user, String text){
        // ....
    }
    @CallbackQueryMapping(dataRegexp = "YOUR_REGULAR_EXPRESSION")
    public UserState callbackQueryMapping(HashedUser user, CallbackQuery callbackQuery){
        // ....
    }
    @ContactMapping(target = ContactMapping.ContactMappingTarget.PHONE_NUMBER)
    public UserState contactMapping(HashedUser user, String phoneNumber){
        // ....
    }
    @LocationMapping
    public UserState locationMapping(HashedUser user, Location location){
        // ....
    }
    @PhotoMapping
    public UserState photoMapping(HashedUser user, PhotoSize photoSize){
        // ....
    }
    
    // ...
}
```
`Note❗`️ Those methods are triggered when the user makes an action to the bot. Inside the method, you can implement the logic to handle the received data. To ensure a coherent user experience, the method should return a `UserState` object representing the user's current state or context. This `UserState` object can store information about the user's last action or interaction.

By consistently returning UserState objects in these mapping methods, you create a structured approach to manage user states, facilitating a more organized and responsive Telegram bot. Customize the logic within each method to suit your bot's specific requirements and enhance the overall user interaction. 🚀🤖

---

# Congratulations! 🎉

Fantastic News! You've completed reading the documentation for `io.github.reflectframework:reflect-telegram-bot`! 🚀 Your commitment to understanding the library and its features is truly commendable.

---

## Example Project: Telegram Bot using **Reflect Telegram Bot**

The **Unit Bot** example project showcases a Telegram bot integrated with Spotify, allowing users to search for music by name. This project is built using `io.github.reflectframework:reflect-telegram-bot`, highlighting the seamless integration of a Telegram bot with external APIs like Spotify.

**Full Source Code:** [View on GitHub](https://github.com/bekzod-murotboyev/unit-bot)

---

## Key Takeaways:

1. **Comprehensive Understanding:** By finishing the documentation, you now possess a comprehensive understanding of `io.github.reflectframework:reflect-telegram-bot`, its functionalities, and how it can enhance your Telegram bot projects.

2. **Skill Enhancement:** Your dedication to learning and exploring the documentation reflects your commitment to honing your skills as a developer working with Telegram bots.

3. **Troubleshooting Mastery:** Armed with the troubleshooting tips and insights provided in the documentation, you are well-equipped to navigate challenges and ensure a smooth development experience.

## What's Next?

1. **Implementation:** It's time to put your newfound knowledge into action! Start implementing the library in your Telegram bot projects and witness the power it brings to your development journey.

2. **Community Engagement:** Join community forums or channels associated with the library. Engage with other developers, share your experiences, and seek support or insights.

3. **Feedback:** If you have any thoughts or suggestions about the documentation, consider providing feedback. Your input could contribute to the continuous improvement of the library's resources.

---

# Key Features 🚀

## Redis-based Auto-Configuration

Harness the power of Redis for seamless state management and caching with the library's built-in auto-configuration. Store user states, preferences, and more in Redis, enhancing the efficiency of your Telegram bot. Enable it by setting `data.redis.repositories.enabled: true` in your YAML configuration:
```yaml
data:
  redis:
    host: REDIS_HOST
    port: REDIS_PORT
    repositories:
      enabled: true   # Default: false
```

---

## Mapping Annotations and Regular Expression Checks 🧐📝

In the Reflect Telegram Bot Library, mapping annotations come equipped with a versatile feature – the `regexp` field. This field allows you to define a regular expression (regexp) pattern that incoming text messages must match for a particular method to be triggered.

### Example: Text Mapping

Consider the `@TextMapping` annotation:

```java
@TextMapping(regexp = "/start")
public UserState startMapping(HashedUser user){
    // ....
}
```
In this example, the `@TextMapping` annotation is configured with `regexp = "/start"`. This means that the `startMapping` method will only be triggered when a user sends a text message containing `/start`. The regular expression acts as a filter, ensuring that the method responds specifically to messages that match the defined pattern.

### Example: Callback Query Mapping

Consider the `@CallbackQueryMapping` annotation:

```java
@CallbackQueryMapping(dataRegexp = "^option_\\d+$", target = CallbackQueryMapping.CallbackQueryMappingTarget.QUERY_DATA)
public UserState optionMapping(HashedUser user, String optionData){
    // ....
}
```
Here, the @CallbackQueryMapping annotation uses dataRegexp = "^option_\\d+$". The method optionMapping will be invoked when a callback query is received with data matching the specified regular expression, such as "option_1", "option_2", and so on.

By utilizing the regexp or dataRegexp field in mapping annotations, you can enforce specific text patterns, adding a layer of control and customization to your bot's interaction logic. This feature is particularly useful when you want methods to respond selectively based on the content of incoming messages. 🧐📝

## Mapping Annotations and User States 🚦🔍

Within the Reflect Telegram Bot Library, mapping annotations include a powerful feature – the `states` field. This field provides the capability to check the user's state every time they make a request. The default value for `states` is an empty array, meaning that user state checking is not enforced by default.

### Example: Text Mapping

Consider the `@TextMapping` annotation:

```java
@TextMapping(regexp = "/start", states = {State.Fields.INIT})
public UserState startMapping(HashedUser user){
    // ....
}
```
In this example, the @TextMapping annotation includes states = {State.INIT}. This configuration ensures that the startMapping method will only be triggered if the user is in the "INIT" state. The states field acts as a filter, allowing methods to respond selectively based on the user's current state.

By leveraging the states field in mapping annotations, you introduce a dynamic element to your bot's interaction logic. Methods can be configured to respond conditionally based on the user's state, providing a tailored and context-aware user experience. 🚦🔍

## Mapping Annotations and Target Fields 📌

In the Reflect Telegram Bot Library, mapping annotations play a crucial role in associating methods with specific types of incoming messages or events. Each mapping annotation comes with its own designated `target` field, providing the required type as a method parameter.

### Example: Contact Mapping

Consider the `@ContactMapping` annotation:

```java
@ContactMapping(target = ContactMapping.ContactMappingTarget.PHONE_NUMBER)
public UserState locationMapping(HashedUser user, String phoneNumber){
    // ....
}
```
In this example, the @ContactMapping annotation specifies that this method is triggered when the user sends a contact to the bot. The Contact's phone number object is declared as a parameter, and it is provided through the target field of the annotation. This ensures that the method receives the necessary data type for handling location-related interactions.

---

## Mapping Annotations and Chat Types 🌐💬

In the Reflect Telegram Bot Library, mapping annotations are equipped with a powerful feature – the `chatTypes` field. This field allows you to explicitly declare the chat types for which a particular method should be triggered. The default chat type is set to private.

### Example: Location Mapping

Consider the `@LocationMapping` annotation:

```java
@LocationMapping(chatTypes = {ChatType.GROUP, ChatType.SUPERGROUP})
public UserState locationMapping(HashedUser user, Location location){
    // ....
}
```
In this example, the @LocationMapping annotation is configured to catch only location events from group and supergroup chats. By setting chatTypes = {ChatType.GROUP, ChatType.SUPERGROUP}, the method locationMapping will only be triggered for location messages in these specified chat types.

---

## i18n Support
Now, your bot is ready to speak multiple languages, providing a personalized experience for users around the world! 🌍🌐
```yml
bot:
  # Other bot configurations...
  i18:
    enabled: true   # Default: false
    key:
      back-button:  # if(bot.i18.enabled) "back-button" else "⬅️ Back"
      contact-button: # if(bot.i18.enabled) "contact-button" else "📲 My Phone" 
      location-button: # if(bot.i18.enabled) "location-button " else "🗺 My Location" 
      back-button-prefix: BACK_
```

`Note❗`️ When enabling i18n support (`bot.i18.enabled: true`), it's essential to integrate Spring's `messages.properties` for efficient management of localized messages. Ensure that you have a `messages.properties` file in your project's resources with translations for the declared keys. 

In the provided example, the keys such as `back-button`, `contact-button`, and `location-button` serve as placeholders for the corresponding localized messages. These keys should be defined in your message.properties file, each associated with the translated text for different languages.

Ensure that your `message.properties` file includes entries like:
```
back-button=Go Back
contact-button=Send Contact
location-button=Share Location
```

---

## Enhanced Messaging with Sender Object 📤
The library introduces a powerful component, `io.github.reflectframework.reflecttelegrambot.components.sender.base.Sender`, designed to streamline the process of sending various types of messages to users on the Telegram platform.
```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.utils.enums.KeyboardType;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Sender sender;

    @TextMapping(regexp = "/start")
    public UserState showStartMenu(HashedUser user) {
        sender.sendMessage(...);  // used to send a SendMessage
        sender.sendWebMessage(...); // used to send a SendMessage with web page button
        sender.editMessageText(...); // used to send a EditMessageText
        sender.editWebMessageText(...); // used to send a EditMessageText with web page button
        sender.sendPhoto(...); // used to send a SendPhoto
        sender.sendDocument(...); // used to send a SendDocument
        sender.sendAudio(...); // used to send a SendAudio
        sender.sendVoice(...); // used to send a SendVoice
        sender.sendVideo(...); // used to send a SendVideo
        sender.sendInvoice(...); // used to send a SendInvoice
        :
        
        ...
    }
    
}
```
Those methods encapsulates the complexities of sending messages, making it straightforward for developers to incorporate various communication features into their Telegram bot applications. Whether it's responding to user queries, providing updates, or delivering important information, sender methods empowers developers to enhance the user experience effortlessly.

For developers looking to send custom messages in their Telegram bot applications, the library provides a powerful tool — `Reflector`. This object enables the direct transmission of custom messages, offering a flexible approach to communication beyond standard text messages.
```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.network.feignclients.telegram.Reflector;
import io.github.reflectframework.reflecttelegrambot.annotations.mappings.TextMapping;
import io.github.reflectframework.reflecttelegrambot.utils.enums.KeyboardType;
import io.github.reflectframework.reflecttelegrambot.entities.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.utils.markers.UserState;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendDocument;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendPhoto;
import org.telegram.telegrambots.meta.api.methods.send.SendMessage;
import org.telegram.telegrambots.meta.api.methods.updatingmessages.EditMessageText;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Reflector reflector;

    @TextMapping(regexp = "/start")
    public UserState showStartMenu(HashedUser user) {
        reflector.sendMessage(new SendMessage(...));  // used to send a SendMessage
        reflector.editMessageText(new EditMessageText(...)); // used to send a EditMessageText
        reflector.sendPhoto(new SendPhoto(...)); // used to send a SendPhoto
        reflector.sendDocument(new SendDocument(...)); // used to send a SendDocument
        
        reflector.getFilePath("fileId"); // used to get file info and full file path
        reflector.getFile("fileId"); // used to get file content as 'feign.Response'
        :
        
        ...
    }
    
}
```
Utilizing `reflector`, developers gain the ability to transmit messages with custom structures or additional fields tailored to their specific requirements. This facilitates the integration of diverse message types, such as media files, interactive buttons, or any other custom content.

---


Kudos on completing this milestone! Your commitment to learning and growing as a developer is truly inspiring. Best of luck with your Telegram bot endeavors! 🌟

**Happy Coding!**

---
*Feel free to customize the message based on your preferences or the specific details of the Telegram bot library you've been exploring.*


*This documentation is proudly developed and maintained by [Bekzodbek Murotboyev!](https://www.linkedin.com/in/bekzodbek-murotboyev/)*

*Connect on [GitHub](https://github.com/bekzod-murotboyev)*

*Connect on [Linkedin](https://www.linkedin.com/in/bekzodbek-murotboyev)*

*Feel free to reach out, ask questions, or provide feedback.*
        

