
# Reflect Telegram Bot Library (Version 1.0.0) 🤖

Developing Telegram bots in Java is a breeze with `io.github.reflectframework:reflect-telegram-bot`! 🚀

## Abstraction of Telegram API Methods

This library provides a high-level abstraction over Telegram bot API methods. Say goodbye to tedious low-level details and focus on your bot's brilliance! 🌟

```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.base.Sender;
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
        sender.sendMessage(user, "📲 Change Phone", KeyboardType.CONTACT);
        return State.SEND_PHONE;
    }
    
}
```

## Event Handling and Callback Abstractions

Handle incoming messages and events effortlessly! The library adopts an event-driven model and magical abstractions, making your bot a responsive chatterbox. 📬

```java
import io.github.reflectframework.reflecttelegrambot.annotations.BotController;
import io.github.reflectframework.reflecttelegrambot.components.sender.base.Sender;
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
import io.github.reflectframework.reflecttelegrambot.components.sender.base.Sender;
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
import io.github.reflectframework.reflecttelegrambot.components.sender.base.Sender;
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

If you choose to use this particular library, it's important to note that it may not fully support alternative methods of receiving updates, such as long polling. With a webhook-centric approach, you would typically only need to inform the library about the domain where it should expect incoming updates, simplifying the configuration process.

# 🚀 Getting started with Reflect Telegram Bot
Note❗️ Library is created based on spring boot starter dependencies. The minimum version of spring boot is 3.0.0 and the minimum version of java is 17. Please make sure you are using exactly same or higher versions!

First of all add dependency to your project with one of options below:

1. Using Maven Central Repository:
```xml
 <dependency>
    <groupId>io.github.reflectframework</groupId>
    <artifactId>reflect-telegram-bot</artifactId>
    <version>1.0.0</version>
</dependency>
```
2. Using Gradle(Short): 
```gradle
implementation 'io.github.reflectframework:reflect-telegram-bot:1.0.0'
```
3. Using Gradle(Kotlin): 
```gradle
implementation("io.github.reflectframework:reflect-telegram-bot:1.0.0")
```
---

### **🏁 Step 1️⃣:** Setting required credentials via application.yml file
```yaml
bot:
  domain: YOUR_DOMAIN  # https://example.com or example.com
  token:  YOUR_BOT_TOKEN  # You can learn how to get it from https://core.telegram.org/bots/faq#how-do-i-create-a-bot
```
As described above the library works only webhook approach, in case, you want your project would use long polling method, this library can't help you! For local working we highle recommend you to use [NGROK](https://ngrok.com/). This is a versatile and popular tool used for exposing local servers to the internet. You can dowmload it from [here](https://ngrok.com/download)!

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
This is simple enum too, but marked as UserLanguage. This helps you to work with multi languges. Here the `code` is one of the static field of `java.util.Locale` class.The `code` must not be wrong!

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
---

# Congratulations! 🎉

Fantastic News! You've completed reading the documentation for `io.github.reflectframework:reflect-telegram-bot`! 🚀 Your commitment to understanding the library and its features is truly commendable.

## Key Takeaways:

1. **Comprehensive Understanding:** By finishing the documentation, you now possess a comprehensive understanding of `io.github.reflectframework:reflect-telegram-bot`, its functionalities, and how it can enhance your Telegram bot projects.

2. **Skill Enhancement:** Your dedication to learning and exploring the documentation reflects your commitment to honing your skills as a developer working with Telegram bots.

3. **Troubleshooting Mastery:** Armed with the troubleshooting tips and insights provided in the documentation, you are well-equipped to navigate challenges and ensure a smooth development experience.

## What's Next?

1. **Implementation:** It's time to put your newfound knowledge into action! Start implementing the library in your Telegram bot projects and witness the power it brings to your development journey.

2. **Community Engagement:** Join community forums or channels associated with the library. Engage with other developers, share your experiences, and seek support or insights.

3. **Feedback:** If you have any thoughts or suggestions about the documentation, consider providing feedback. Your input could contribute to the continuous improvement of the library's resources.

Kudos on completing this milestone! Your commitment to learning and growing as a developer is truly inspiring. Best of luck with your Telegram bot endeavors! 🌟

**Happy Coding!**

---
*Feel free to customize the message based on your preferences or the specific details of the Telegram bot library you've been exploring.*
        

