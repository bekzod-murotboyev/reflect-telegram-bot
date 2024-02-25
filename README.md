
# Reflect Telegram Bot Library (Version 1.0.0) 🤖

Developing Telegram bots in Java is a breeze with `io.github.reflectframework:reflect-telegram-bot:1.0.0`! 🚀

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
