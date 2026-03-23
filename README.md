
# Reflect Telegram Bot Library (Version 1.8.1) 🤖

Developing Telegram bots in Java is a breeze with `io.github.reflectframework:reflect-telegram-bot`! 🚀

## One Annotation Per Telegram Update Type

The fastest way to understand the library is to see how every incoming Telegram update maps directly to a controller method. No manual update parsing, no `if/else` trees, no giant dispatcher classes. 🌟

```java
import io.github.reflectframework.reflecttelegrambot.annotation.BotController;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.CallbackQueryMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.ContactMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.LocationMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.TextMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.animation.AnimationMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.audio.AudioMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.document.DocumentMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.photo.PhotoMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.video.VideoMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.voice.VoiceMapping;
import io.github.reflectframework.reflecttelegrambot.component.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import lombok.RequiredArgsConstructor;
import org.telegram.telegrambots.meta.api.objects.CallbackQuery;
import org.telegram.telegrambots.meta.api.objects.Location;
import org.telegram.telegrambots.meta.api.objects.Audio;
import org.telegram.telegrambots.meta.api.objects.Document;
import org.telegram.telegrambots.meta.api.objects.Video;
import org.telegram.telegrambots.meta.api.objects.contact.Contact;
import org.telegram.telegrambots.meta.api.objects.games.Animation;
import org.telegram.telegrambots.meta.api.objects.message.Message;
import org.telegram.telegrambots.meta.api.objects.photo.PhotoSize;
import org.telegram.telegrambots.meta.api.objects.Voice;

@BotController
@RequiredArgsConstructor
public class UpdateMappingsController {

    private final Sender sender;

    @TextMapping(regexp = "/start")
    public String start(HashedUser user) {
        sender.sendMessage(user)
                .text("Choose what you want to send: text, contact, location or media.")
                .inlineKeyboardRow("Open profile", "Open uploads")
                .send();
        return "MAIN_MENU";
    }

    @TextMapping(regexp = "settings|help", states = "MAIN_MENU", target = TextMapping.TextMappingTarget.TEXT)
    public void textCommand(HashedUser user, String text) {
        sender.sendMessage(user)
                .text("TextMapping matched: " + text)
                .send();
    }

    @CallbackQueryMapping(dataRegexp = "profile|uploads", target = CallbackQueryMapping.CallbackQueryMappingTarget.QUERY_DATA)
    public void callbackData(HashedUser user, String data) {
        sender.editMessageText(user)
                .text("CallbackQueryMapping matched: " + data)
                .send();
    }

    @CallbackQueryMapping(textRegexp = "Open .*", target = CallbackQueryMapping.CallbackQueryMappingTarget.CALLBACK_QUERY)
    public void callbackObject(HashedUser user, CallbackQuery callbackQuery) {
        sender.sendMessage(user)
                .text("CallbackQueryMapping matched messageId: " + callbackQuery.getMessage().getMessageId())
                .send();
    }

    @ContactMapping(states = "WAITING_PHONE", target = ContactMapping.ContactMappingTarget.PHONE_NUMBER)
    public String phoneNumber(HashedUser user, String phoneNumber) {
        sender.sendMessage(user)
                .text("Phone saved: " + phoneNumber)
                .send();
        return "PROFILE_READY";
    }

    @ContactMapping(target = ContactMapping.ContactMappingTarget.CONTACT)
    public void contactObject(HashedUser user, Contact contact) {
        sender.sendMessage(user)
                .text("ContactMapping matched: " + contact.getFirstName())
                .send();
    }

    @LocationMapping(states = "WAITING_LOCATION", target = LocationMapping.LocationMappingTarget.LOCATION)
    public void location(HashedUser user, Location location) {
        sender.sendMessage(user)
                .text("Location received: " + location.getLatitude() + ", " + location.getLongitude())
                .send();
    }

    @PhotoMapping(states = "WAITING_MEDIA", target = PhotoMapping.PhotoMappingTarget.PHOTO_SIZE)
    public void photo(HashedUser user, PhotoSize photo) {
        sender.sendMessage(user)
                .text("PhotoMapping matched: " + photo.getFileUniqueId())
                .send();
    }

    @VoiceMapping(states = "WAITING_MEDIA", target = VoiceMapping.VoiceMappingTarget.VOICE)
    public void voice(HashedUser user, Voice voice) {
        sender.sendMessage(user)
                .text("VoiceMapping matched: " + voice.getDuration() + " sec")
                .send();
    }

    @AudioMapping(states = "WAITING_MEDIA", target = AudioMapping.AudioMappingTarget.AUDIO)
    public void audio(HashedUser user, Audio audio) {
        sender.sendMessage(user)
                .text("AudioMapping matched: " + audio.getTitle())
                .send();
    }

    @VideoMapping(states = "WAITING_MEDIA", target = VideoMapping.VideoMappingTarget.VIDEO)
    public void video(HashedUser user, Video video) {
        sender.sendMessage(user)
                .text("VideoMapping matched: " + video.getFileName())
                .send();
    }

    @DocumentMapping(states = "WAITING_MEDIA", target = DocumentMapping.DocumentMappingTarget.DOCUMENT)
    public void document(HashedUser user, Document document) {
        sender.sendMessage(user)
                .text("DocumentMapping matched: " + document.getFileName())
                .send();
    }

    @AnimationMapping(states = "WAITING_MEDIA", target = AnimationMapping.AnimationMappingTarget.ANIMATION)
    public void animation(HashedUser user, Animation animation) {
        sender.sendMessage(user)
                .text("AnimationMapping matched: " + animation.getFileName())
                .send();
    }

    @TextMapping(regexp = ".*", target = TextMapping.TextMappingTarget.MESSAGE)
    public void fallbackMessage(HashedUser user, Message message) {
        sender.sendMessage(user)
                .text("Unhandled update in state: " + user.getState() + ", messageId=" + message.getMessageId())
                .send();
    }
}
```

## Media Group Mapping Without Manual Aggregation

Albums and grouped uploads are where bot code usually turns messy. Reflect Telegram Bot gives you dedicated annotations for every media-group case, so your controller can work with the queued group instead of rebuilding it yourself. 📦

```java
import io.github.reflectframework.reflecttelegrambot.annotation.BotController;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.MediaGroupMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.animation.AnimationGroupMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.audio.AudioGroupMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.document.DocumentGroupMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.photo.PhotoGroupMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.video.VideoGroupMapping;
import io.github.reflectframework.reflecttelegrambot.component.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.service.mediagroup.AbstractMediaGroupQueue;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class MediaGroupController {

    private final Sender sender;

    @MediaGroupMapping(states = "WAITING_ALBUM")
    public void anyAlbum(HashedUser user, AbstractMediaGroupQueue<?> mediaGroup) {
        sender.sendMessage(user)
                .text("MediaGroupMapping collected album: " + mediaGroup.getMediaGroupId())
                .send();
    }

    @PhotoGroupMapping(states = "WAITING_PHOTO_ALBUM")
    public void photoAlbum(HashedUser user, AbstractMediaGroupQueue<?> photoGroup) {
        sender.sendMessage(user)
                .text("PhotoGroupMapping collected group: " + photoGroup.getMediaGroupId())
                .send();
    }

    @AudioGroupMapping(states = "WAITING_AUDIO_ALBUM")
    public void audioAlbum(HashedUser user, AbstractMediaGroupQueue<?> audioGroup) {
        sender.sendMessage(user)
                .text("AudioGroupMapping collected group: " + audioGroup.getMediaGroupId())
                .send();
    }

    @VideoGroupMapping(states = "WAITING_VIDEO_ALBUM")
    public void videoAlbum(HashedUser user, AbstractMediaGroupQueue<?> videoGroup) {
        sender.sendMessage(user)
                .text("VideoGroupMapping collected group: " + videoGroup.getMediaGroupId())
                .send();
    }

    @DocumentGroupMapping(states = "WAITING_DOCUMENT_ALBUM")
    public void documentAlbum(HashedUser user, AbstractMediaGroupQueue<?> documentGroup) {
        sender.sendMessage(user)
                .text("DocumentGroupMapping collected group: " + documentGroup.getMediaGroupId())
                .send();
    }

    @AnimationGroupMapping(states = "WAITING_ANIMATION_ALBUM")
    public void animationAlbum(HashedUser user, AbstractMediaGroupQueue<?> animationGroup) {
        sender.sendMessage(user)
                .text("AnimationGroupMapping collected group: " + animationGroup.getMediaGroupId())
                .send();
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
`Note❗️` The library targets Spring Boot 4.0.3+ and Java 21+. Please use the same or newer versions.

First of all add dependency to your project with one of options below:

1. Using Maven Central Repository:
```xml
<dependency>
    <groupId>io.github.reflectframework</groupId>
    <artifactId>reflect-telegram-bot</artifactId>
    <version>1.8.1</version>
</dependency>
```
2. Using Gradle(Short):
```gradle
implementation 'io.github.reflectframework:reflect-telegram-bot:1.8.1'
```
3. Using Gradle(Kotlin):
```gradle
implementation("io.github.reflectframework:reflect-telegram-bot:1.8.1")
```
---

### **🏁 Step 1️⃣:** Setting required credentials via application.yml file
```yaml
bot:
  mode: development  # By default 'none' - turned off, in 'development' mode uses telegram long polling method, if specified 'production', telegram webhook method will be autoconfigured
  domain: YOUR_DOMAIN  # https://example.com or example.com; Specify this property when 'mode' marked as 'production';
  token:  YOUR_BOT_TOKEN  # You can learn how to get it from https://core.telegram.org/bots/faq#how-do-i-create-a-bot;
  username: YOUR_BOT_USERNAME  # Optional bot username from BotFather, for example my_sample_bot
  i18n: false  # Enable MessageSource-based translation support
```
Autoconfiguration is enabled by default. You do **not** need `@EnableBot` anymore; just add the dependency and set `bot.*` properties.
In case, you want to check production mode locally, we highly recommend you to use [NGROK](https://ngrok.com/). This is a versatile and popular tool used for exposing local servers to the internet. You can download it from [here!](https://ngrok.com/download)

---

### **🏁 Step 2️⃣:** Creating Bot Controller
_The library now ships with a default in-memory `TelegramUserDetailsService`, so you can start without creating a user entity or a database service._
```java
import io.github.reflectframework.reflecttelegrambot.annotation.BotController;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.CallbackQueryMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.ContactMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.LocationMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.TextMapping;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.media.photo.PhotoMapping;
import io.github.reflectframework.reflecttelegrambot.component.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import lombok.RequiredArgsConstructor;
import org.telegram.telegrambots.meta.api.objects.CallbackQuery;
import org.telegram.telegrambots.meta.api.objects.Location;
import org.telegram.telegrambots.meta.api.objects.photosizes.PhotoSize;

@BotController
@RequiredArgsConstructor
public class Controller {

    private final Sender sender;

    @TextMapping(regexp = "/start")
    public void start(HashedUser user) {
        sender.sendMessage(user)
                .text("Hello from Reflect Telegram Bot")
                .send();
    }

    @CallbackQueryMapping(dataRegexp = "YOUR_REGULAR_EXPRESSION")
    public void callbackQueryMapping(HashedUser user, CallbackQuery callbackQuery){
        // ....
    }

    @ContactMapping(target = ContactMapping.ContactMappingTarget.PHONE_NUMBER)
    public void contactMapping(HashedUser user, String phoneNumber){
        // ....
    }

    @LocationMapping
    public void locationMapping(HashedUser user, Location location){
        // ....
    }

    @PhotoMapping
    public void photoMapping(HashedUser user, PhotoSize photoSize){
        // ....
    }
}
```
That is enough for a fast local start. In development mode, the library stores users in memory automatically, so you can test mappings, senders, and i18n without preparing a database first.
# Congratulations! 🎉

Your bot is now up and running with the Quick Start setup. From here, you can either keep building with the default in-memory user storage for fast experiments, or move on to a custom persistent user model for production-ready projects.

---

## Custom User Details Implementation

For production projects, you will usually want to persist users in your own database. In that case, provide your own implementations of `TelegramUserDetails` and `TelegramUserDetailsService`. Once your bean exists, the library's default in-memory implementation is skipped automatically.

### **Creating type for User State as Enum** `(Optional)`
```java
import lombok.experimental.FieldNameConstants;

@FieldNameConstants(onlyExplicitlyIncluded = true)
public enum State {
    @FieldNameConstants.Include INIT,
    @FieldNameConstants.Include SEND_PHONE,
    @FieldNameConstants.Include MAIN_MENU
}
```
This is a simple enum. Persist or return it as `state.name()` when implementing `TelegramUserDetails`, and use its names in mapping `states = {...}` filters. [Lombok's](https://projectlombok.org/) [@FieldNameConstants](https://projectlombok.org/features/experimental/FieldNameConstants#:~:text=The%20%40FieldNameConstants%20annotation%20generates%20an,static%20final%20%2C%20of%20type%20java.) annotation is useful here for generating matching constant names.

### **Creating type for User Language as Enum** `(Optional)`
```java

public enum Language {
    EN("en"),
    RU("ru");

    private final String code;

    Language(String code) {
        this.code = code;
    }

    public String getCode() {
        return code;
    }
}
```
This is a simple enum. Store its code, such as `en`, `ru`, or `uz`, as `languageCode`. The code is the suffix of one of the `messages.properties` files, for example `messages_ru.properties`.

---
### **Creating your User Entity**
```java
import io.github.reflectframework.reflecttelegrambot.entity.user.TelegramUserDetails;
import jakarta.annotation.Nonnull;
import jakarta.annotation.Nullable;
import jakarta.persistence.*;

@Entity
public class UserEntity implements TelegramUserDetails {

    private String name;

    private long chatId;

    private State state;

    private String languageCode;

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    public UserEntity(String name, long chatId, State state, String languageCode) {
        this.name = name;
        this.chatId = chatId;
        this.state = state;
        this.languageCode = languageCode;
    }

    public UserEntity() {}

    @Nonnull
    @Override
    public Long getChatId() {
        return this.chatId;
    }

    @Nullable
    @Override
    public String getState() {
        return this.state.name(); // OR NULL
    }

    @Nullable
    @Override
    public String getLanguageCode() {
        return this.languageCode; // OR NULL
    }
}
```
`TelegramUserDetails` requires only three methods: `getChatId()`, `getState()`, and `getLanguageCode()`. `getLanguageCode()` is optional, so you can return `null` if you do not use i18n.

---
### **Creating your User Service**
```java
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.entity.user.TelegramUserDetails;
import io.github.reflectframework.reflecttelegrambot.service.user.TelegramUserDetailsService;
import jakarta.annotation.Nonnull;
import jakarta.annotation.Nullable;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.telegram.telegrambots.meta.api.objects.User;

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
        return userRepository.save(new UserEntity(
                user.getFirstName(),
                chatId,
                State.INIT,
                Language.EN.getCode()
        ));
    }

    @Override
    public void update(@Nonnull HashedUser user) {
        UserEntity entity = userRepository.findByChatId(user.getChatId());
        if (entity == null) {
            return;
        }
        entity.setState(user.getState() != null ? State.valueOf(user.getState()) : null);
        entity.setLanguageCode(user.getLanguageCode() != null ? user.getLanguageCode() : null);
        userRepository.save(entity);
    }
}
```
`TelegramUserDetailsService` has only three responsibilities:
- `findByChatId(long chatId)` should return the stored user or `null` for a new user
- `save(User user, long chatId)` should create a new user when the chat is seen for the first time
- `update(HashedUser user)` should persist state and language changes after handlers return a new `String`

---
### **Optional: Allowing Telegram Webhook in Spring Security**

If your **Spring Boot** application uses **Spring Security**, you need to explicitly allow `POST` requests for the **Telegram Bot Webhook URL**. Otherwise, incoming updates from Telegram may be blocked.

#### ✅ How to Permit the Webhook URL?

You can configure your **Spring Security filter chain** to permit `POST` requests to the **Telegram Webhook URL**:

```java
import static io.github.reflectframework.reflecttelegrambot.util.constant.Constant.TELEGRAM_WEBHOOK_PATH;

@Configuration
@EnableMethodSecurity
public class WebSecurityConfiguration {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests(requestMatcherRegistry -> requestMatcherRegistry
                        .requestMatchers(HttpMethod.POST, TELEGRAM_WEBHOOK_PATH).permitAll()
                        .anyRequest().authenticated());
        return http.build();
    }
}
```

---
### **Handler Return Values**
`Note❗`️ Mapping methods are triggered when the user sends an update to the bot. Inside the method, you can implement the logic to handle the received data. To ensure a coherent user experience, the method should return a `String` object representing the user's current state or context. This `String` object can store information about the user's last action or interaction.

By consistently returning String objects in these mapping methods, you create a structured approach to manage user states, facilitating a more organized and responsive Telegram bot. Customize the logic within each method to suit your bot's specific requirements and enhance the overall user interaction. 🚀🤖

---

# Key Features 🚀

## Redis (Lettuce, Low-Level)

The library uses a lightweight Lettuce client. Enable it via:
```yaml
bot:
  redis:
    enabled: true
    host: REDIS_HOST
    port: REDIS_PORT
    database: 0
    password: ""
    ssl: false
    timeout: PT5S
    ttl-seconds: 86400   # use -1 to disable TTL
    key-prefix: "bot:user:"
    pool:
      max-active: 8      # use -1 for unlimited
      max-idle: 8        # use -1 for unlimited
      min-idle: 0
      max-wait: PT1S     # negative means wait indefinitely
```

---

## Mapping Annotations and Regular Expression Checks 🧐📝

In the Reflect Telegram Bot Library, mapping annotations come equipped with a versatile feature – the `regexp` field. This field allows you to define a regular expression (regexp) pattern that incoming text messages must match for a particular method to be triggered.

#### Example: Text Mapping

Consider the `@TextMapping` annotation:

```java
@TextMapping(regexp = "/start")
public String startMapping(HashedUser user){
    // ....
}
```
In this example, the `@TextMapping` annotation is configured with `regexp = "/start"`. This means that the `startMapping` method will only be triggered when a user sends a text message containing `/start`. The regular expression acts as a filter, ensuring that the method responds specifically to messages that match the defined pattern.

#### Example: Callback Query Mapping

Consider the `@CallbackQueryMapping` annotation:

```java
@CallbackQueryMapping(dataRegexp = "^option_\\d+$", target = CallbackQueryMapping.CallbackQueryMappingTarget.QUERY_DATA)
public String optionMapping(HashedUser user, String optionData){
    // ....
}
```
Here, the @CallbackQueryMapping annotation uses dataRegexp = "^option_\\d+$". The method optionMapping will be invoked when a callback query is received with data matching the specified regular expression, such as "option_1", "option_2", and so on.

By utilizing the regexp or dataRegexp field in mapping annotations, you can enforce specific text patterns, adding a layer of control and customization to your bot's interaction logic. This feature is particularly useful when you want methods to respond selectively based on the content of incoming messages. 🧐📝

## Mapping Annotations and Dynamic Regular Expression Translation  🌟✨
This feature is particularly useful in applications that need to support multiple languages or require dynamic handling of regular expressions based on user context.

#### Example: Text Mapping

Consider the `@TextMapping` annotation:
```java
@TextMapping(regexp = "text-basket", translateRegexp = true)
public String textBasketMapping(HashedUser user){
    // ....
}
```

The `translateTextRegexp` parameter, when set to true, signifies that the regular expression provided in textRegexp should undergo translation. If your application supports multiple languages, translateTextRegexp = true might mean that "text-basket" should be translated to match the user's language preference before applying the regular expression match.

### Special Feature: Prefix and Suffix Translation 🎯🧩
If you specify a prefix and suffix of  `%`, the library will translate only the content inside the character sequence. The rest of the pattern will be treated as a regular expression, maintaining its integrity.

#### Example: Callback Query Mapping
Consider the `@CallbackQueryMapping` annotation:
```java
@CallbackQueryMapping(textRegexp = "^%option%[\\d]*", translateTextRegexp = true)
public String optionMapping(HashedUser user, String optionData){
    // ....
}
```
In this example, only `option` will be translated based on your i18n settings, while the `^` and `[\\d]*` symbols will be retained as part of the regular expression. This feature ensures that your regex patterns remain precise and functional while supporting multiple languages! 🌐🗣️

Embrace the magic of seamless text translation within your regex patterns, making your bot smarter and more user-friendly across different languages! 🌍💬✨


## Mapping Annotations and User States 🚦🔍

Within the Reflect Telegram Bot Library, mapping annotations include a powerful feature – the `states` field. This field provides the capability to check the user's state every time they make a request. The default value for `states` is an empty array, meaning that user state checking is not enforced by default.

#### Example: Text Mapping

Consider the `@TextMapping` annotation:

```java
@TextMapping(regexp = "/start", states = {State.Fields.INIT})
public String startMapping(HashedUser user){
    // ....
}
```
In this example, the `@TextMapping` annotation includes `states = {State.Fields.INIT}`. This configuration ensures that the `startMapping` method will only be triggered if the user is in the `"INIT"` state. The `states` field acts as a filter, allowing methods to respond selectively based on the user's current state.

By leveraging the states field in mapping annotations, you introduce a dynamic element to your bot's interaction logic. Methods can be configured to respond conditionally based on the user's state, providing a tailored and context-aware user experience. 🚦🔍

## Mapping Annotations and Target Fields 📌

In the Reflect Telegram Bot Library, mapping annotations play a crucial role in associating methods with specific types of incoming messages or events. Each mapping annotation comes with its own designated `target` field, providing the required type as a method parameter.

#### Example: Contact Mapping

Consider the `@ContactMapping` annotation:

```java
@ContactMapping(target = ContactMapping.ContactMappingTarget.PHONE_NUMBER)
public String locationMapping(HashedUser user, String phoneNumber){
    // ....
}
```
In this example, the @ContactMapping annotation specifies that this method is triggered when the user sends a contact to the bot. The Contact's phone number object is declared as a parameter, and it is provided through the target field of the annotation. This ensures that the method receives the necessary data type for handling location-related interactions.

---

## Mapping Annotations and Chat Types 🌐💬

In the Reflect Telegram Bot Library, mapping annotations are equipped with a powerful feature – the `chatTypes` field. This field allows you to explicitly declare the chat types for which a particular method should be triggered. The default chat type is set to private.

#### Example: Location Mapping

Consider the `@LocationMapping` annotation:

```java
@LocationMapping(chatTypes = {ChatType.GROUP, ChatType.SUPERGROUP})
public String locationMapping(HashedUser user, Location location){
    // ....
}
```
In this example, the @LocationMapping annotation is configured to catch only location events from group and supergroup chats. By setting chatTypes = {ChatType.GROUP, ChatType.SUPERGROUP}, the method locationMapping will only be triggered for location messages in these specified chat types.

---

## 📦 Handling Media Groups in a Single Method Invocation

The library supports efficient handling of media groups across multiple types (photos, animations, videos, audio, and documents) in a single bot controller method. This eliminates the need to process media files individually, making bulk media handling seamless.

### 🎯 Supported Media Group Annotations

The following annotations allow bots to receive specific types of media groups in a single method:

| Annotation               | Method Parameter         | Description |
|--------------------------|-------------------------|-------------|
| `@AudioGroupMapping`    | `AudioGroupQueue`      | Handles audio groups |
| `@AnimationGroupMapping`| `AnimationGroupQueue`  | Handles animation groups |
| `@DocumentGroupMapping` | `DocumentGroupQueue`   | Handles document groups |
| `@PhotoGroupMapping`    | `PhotoGroupQueue`      | Handles photo groups |
| `@VideoGroupMapping`    | `VideoGroupQueue`      | Handles video groups |

These annotations ensure that **each method only receives the corresponding media type**, making media handling more efficient and organized.

### 🌍 **Universal Media Group Handler**

For cases where a bot needs to receive **any type of media** in a single method, the new `@MediaGroupMapping` annotation is available:

| Annotation            | Method Parameter   | Description |
|----------------------|-------------------|-------------|
| `@MediaGroupMapping` | `MediaGroupQueue` | Catches any type of media group |

This allows developers to handle mixed media uploads efficiently.

### 📨 **Handling Media Messages**

In addition to receiving media groups, all media mapping annotations now support a **target type `MESSAGE`**, which allows methods to accept `MessageGroupQueue`.

| Annotation               | Target Type  | Method Parameter    | Description |
|--------------------------|-------------|--------------------|-------------|
| `@AudioGroupMapping`    | `MESSAGE`   | `MessageGroupQueue` | Handles audio messages |
| `@AnimationGroupMapping`| `MESSAGE`   | `MessageGroupQueue` | Handles animation messages |
| `@DocumentGroupMapping` | `MESSAGE`   | `MessageGroupQueue` | Handles document messages |
| `@PhotoGroupMapping`    | `MESSAGE`   | `MessageGroupQueue` | Handles photo messages |
| `@VideoGroupMapping`    | `MESSAGE`   | `MessageGroupQueue` | Handles video messages |
| `@MediaGroupMapping`    | `MESSAGE`   | `MessageGroupQueue` | Handles any media message |

#### 🔹 **What is `MessageGroupQueue`?**
- This queue allows developers to **take messages from Telegram updates** rather than just media files.
- Provides a **more flexible way** to handle incoming updates from users.

#### ✅ Example Usage

Below is an example of handling a group of photos, animations, videos, audios and documents:

```java
@PhotoGroupMapping
public String receiveBulkPhotos(HashedUser user, PhotoGroupQueue queue) {
    for (PhotoSize photoSize : queue.takeAsList()) {
        System.out.println(photoSize.getFileId());
    }
    return user.getState();
}

@AnimationGroupMapping
public String receiveBulkAnimations(HashedUser user, AnimationGroupQueue queue) {
    for (Animation animation : queue.takeAsList()) {
        System.out.println(animation.getFileId());
    }
    return user.getState();
}

@VideoGroupMapping
public String receiveBulkVideos(HashedUser user, VideoGroupQueue queue) {
    for (Video video : queue.takeAsList()) {
        System.out.println(video.getFileId());
    }
    return user.getState();
}

@AudioGroupMapping
public String receiveBulkAudio(HashedUser user, AudioGroupQueue queue) {
    for (Audio audio : queue.takeAsList()) {
        System.out.println(audio.getFileId());
    }
    return user.getState();
}

@DocumentGroupMapping
public String receiveBulkDocuments(HashedUser user, DocumentGroupQueue queue) {
    for (Document document : queue.takeAsList()) {
        System.out.println(document.getFileId());
    }
    return user.getState();
}

@MediaGroupMapping
public String receiveBulkMedia(HashedUser user, MediaGroupQueue queue) {
    for (Media media : queue.takeAsList()) {
        System.out.println(media.getFileId());
    }
    return user.getState();
}
```

### ⚙️ Configuring Media Group Scheduler

To manage media group processing more efficiently, the library provides a configurable property for setting the scheduler thread pool size:
```
bot:
  properties:
    media-group-scheduler-pool-size: 1
```
- media-group-scheduler-pool-size: Defines the number of threads allocated for processing media groups.
- Increasing this value can improve parallel processing but requires sufficient system resources.
- Default value: 1

### 🛠 Key Benefits
- Unified Processing: Handle entire media groups in a single method.
- Simplified Development: No need to track or manage individual media items separately.
- Efficient Queueing: Media items are automatically grouped and passed as a collection.
- Configurable Performance: Adjust media group processing concurrency with a dedicated thread pool.
- State Management: Easily integrate with existing user state handling.

This feature significantly enhances the way Telegram bots process media uploads, making bulk handling easier and more efficient! 🚀

---

## i18n Support
Now, your bot is ready to speak multiple languages, providing a personalized experience for users around the world! 🌍🌐
```yml
bot:
  # Other bot configurations...
  i18n: true   # Default: false
```

`Note❗`️ When enabling i18n support (`bot.i18n: true`), it's essential to integrate Spring's `messages.properties` for efficient management of localized messages. Ensure that you have a `messages.properties` file in your project's resources with translations for the declared keys.

---

## Enhanced Messaging with Sender Object 📤
The library introduces a powerful component, `io.github.reflectframework.reflecttelegrambot.component.sender.Sender`, designed to streamline the process of sending various types of messages to users on the Telegram platform.
```java
import io.github.reflectframework.reflecttelegrambot.annotation.BotController;
import io.github.reflectframework.reflecttelegrambot.component.sender.Sender;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.TextMapping;
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Sender sender;

    @TextMapping(regexp = "/start")
    public String showStartMenu(HashedUser user) {
        sender.sendMessage(...);  // builder for SendMessage
        sender.editMessageText(...); // builder for EditMessageText
        sender.sendLocation(...); // builder for SendLocation
        sender.forwardMessage(...); // builder for ForwardMessage
        sender.deleteMessage(...); // builder for DeleteMessage
        sender.sendPhoto(...); // builder for SendPhoto
        sender.sendAnimation(...); // builder for SendAnimation
        sender.sendDocument(...); // used to send a document
        sender.sendAudio(...); // used to send an audio
        sender.sendVoice(...); // used to send a voice
        sender.sendVideo(...); // builder for SendVideo
        sender.sendMediaGroup(...); // builder for SendMediaGroup
        sender.sendInvoice(...); // builder for SendInvoice
        sender.createInvoiceLink(...); // builder for CreateInvoiceLink
        :
        
        ...
    }
    
}
```
Those methods encapsulate the complexities of sending messages, making it straightforward for developers to incorporate various communication features into their Telegram bot applications. Whether it's responding to user queries, providing updates, or delivering important information, sender methods empower developers to enhance the user experience effortlessly.

Builder-based APIs now cover:

- `sendMessage(...)`
- `editMessageText(...)`
- `sendLocation(...)`
- `forwardMessage()`
- `deleteMessage()`
- `sendPhoto(...)`
- `sendAnimation(...)`
- `sendVideo(...)`
- `sendVoice(...)`
- `sendAudio(...)`
- `sendDocument(...)`
- `sendMediaGroup(...)`
- `sendInvoice(...)`
- `createInvoiceLink()`

Always complete builder chains with `.send()`. The builder-returning methods are annotated with `@CheckReturnValue`, so IDEs can warn when the returned builder is ignored.

### Builder Examples

```java
sender.sendMessage(user)
        .text("welcome.message", user.getName())
        .inlineKeyboardRow("Open", "Settings")
        .send();

sender.editMessageText(user)
        .text("menu.updated", user.getName())
        .send();

sender.sendLocation(user)
        .latitude(41.3111)
        .longitude(69.2797)
        .horizontalAccuracy(25.0)
        .send();

sender.sendPhoto(user)
        .photo("telegram-file-id")
        .caption("photo.caption", user.getName())
        .send();

sender.sendPhoto(user)
        .photo(new InputFile("https://example.com/image.jpg"))
        .send();

sender.sendAnimation(user)
        .animation("telegram-file-id")
        .caption("animation.caption", user.getName())
        .send();

sender.sendVideo(user)
        .video("telegram-file-id")
        .caption("video.caption", user.getName())
        .send();

sender.sendVoice(user)
        .voice("telegram-file-id")
        .caption("voice.caption", user.getName())
        .send();

sender.sendAudio(user)
        .audio("telegram-file-id")
        .title("audio.title", user.getName())
        .caption("audio.caption", user.getName())
        .send();

sender.sendDocument(user)
        .document("telegram-file-id")
        .caption("document.caption", user.getName())
        .send();

sender.sendMediaGroup(user)
        .media(new InputMediaPhoto("photo-file-id"))
        .media(new InputMediaAnimation("animation-file-id"))
        .caption("album.caption", user.getName())
        .send();

sender.forwardMessage()
        .chatId(user.getChatId())
        .fromChatId(sourceChatId)
        .messageId(sourceMessageId)
        .send();

sender.deleteMessage()
        .chatId(user.getChatId())
        .messageId(user.getLastMessageId())
        .send();

sender.sendInvoice(user)
        .title("invoice.title", orderId)
        .description("invoice.description", orderId)
        .payload("order-" + orderId)
        .providerToken(providerToken)
        .currency(InvoiceCurrency.UZS)
        .prices(List.of(new LabeledPrice("Order", amount)))
        .send();

String link = sender.createInvoiceLink(user)
        .title("invoice.title", orderId)
        .description("invoice.description", orderId)
        .payload("order-" + orderId)
        .providerToken(providerToken)
        .currency(InvoiceCurrency.UZS)
        .prices(List.of(new LabeledPrice("Order", amount)))
        .send();
```

### Translation Arguments

Builder text methods accept `Object... args`. These arguments are used both for i18n resolution and for default formatting when translation is disabled or language is missing.

For optional text fields, such as photo captions, builders resolve them safely only when a value is present.

```java
sender.sendMessage(user)
        .text("Hello {0}", user.getName())
        .send();

sender.sendPhoto(user)
        .photo("telegram-file-id")
        .caption("Photo for {0}", user.getName())
        .send();

sender.sendAnimation(user)
        .animation("telegram-file-id")
        .caption("Animation for {0}", user.getName())
        .send();

sender.sendInvoice(user)
        .title("invoice.title", orderId)
        .description("invoice.description", orderId, totalAmount)
        .payload("invoice-" + orderId)
        .providerToken(providerToken)
        .currency(InvoiceCurrency.UZS)
        .prices(prices)
        .send();
```

For developers looking to send custom messages in their Telegram bot applications, the library provides a powerful tool — `Reflector`. This object enables the direct transmission of custom messages, offering a flexible approach to communication beyond standard text messages.
```java
import io.github.reflectframework.reflecttelegrambot.annotation.BotController;
import io.github.reflectframework.reflecttelegrambot.network.client.Reflector;
import io.github.reflectframework.reflecttelegrambot.annotation.mapping.TextMapping;
import io.github.reflectframework.reflecttelegrambot.entity.user.HashedUser;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendMediaGroup;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendDocument;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendAnimation;
import io.github.reflectframework.reflecttelegrambot.network.payload.telegram.request.SendPhoto;
import org.telegram.telegrambots.meta.api.methods.send.SendMessage;
import org.telegram.telegrambots.meta.api.methods.updatingmessages.EditMessageText;
import org.telegram.telegrambots.meta.api.objects.InputFile;
import org.telegram.telegrambots.meta.api.objects.media.InputMediaAnimation;
import org.telegram.telegrambots.meta.api.objects.media.InputMediaPhoto;
import lombok.RequiredArgsConstructor;

@BotController
@RequiredArgsConstructor
public class RegisterController {
    
    private final Reflector reflector;

    @TextMapping(regexp = "/start")
    public String showStartMenu(HashedUser user) {
        reflector.sendMessage(new SendMessage(user.getChatId().toString(), "Hello"));

        EditMessageText editMessageText = new EditMessageText("Updated text");
        editMessageText.setChatId(user.getChatId());
        editMessageText.setMessageId(user.getLastMessageId());
        reflector.editMessageText(editMessageText);

        SendPhoto sendPhoto = new SendPhoto();
        sendPhoto.setChatId(user.getChatId());
        sendPhoto.setPhoto(new InputFile("file-id-or-url"));
        reflector.sendPhoto(sendPhoto);

        SendAnimation sendAnimation = new SendAnimation();
        sendAnimation.setChatId(user.getChatId());
        sendAnimation.setAnimation(new InputFile("file-id-or-url"));
        reflector.sendAnimation(sendAnimation);

        SendDocument sendDocument = new SendDocument();
        sendDocument.setChatId(user.getChatId());
        sendDocument.setDocument(new InputFile("file-id-or-url"));
        reflector.sendDocument(sendDocument);

        SendMediaGroup sendMediaGroup = new SendMediaGroup();
        sendMediaGroup.setChatId(user.getChatId());
        sendMediaGroup.setMedias(List.of(
                new InputMediaPhoto("photo-file-id"),
                new InputMediaAnimation("animation-file-id")
        ));
        reflector.sendMediaGroup(sendMediaGroup);
        
        reflector.getFilePath("fileId"); // used to get file info and full file path
        reflector.getFile("fileId"); // used to get file content as byte[]
        :
        
        ...
    }
    
}
```
Utilizing `reflector`, developers gain the ability to transmit messages with custom structures or additional fields tailored to their specific requirements. This facilitates the integration of diverse message types, such as media files, interactive buttons, or any other custom content.

---

# Congratulations! 🎉

Fantastic News! You've completed reading the documentation for `io.github.reflectframework:reflect-telegram-bot`! 🚀 Your commitment to understanding the library and its features is truly commendable. Kudos on completing this milestone! Your commitment to learning and growing as a developer is truly inspiring. Best of luck with your Telegram bot endeavors! 🌟

**Happy Coding!**

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
*Feel free to customize the message based on your preferences or the specific details of the Telegram bot library you've been exploring.*


*This library is proudly developed and maintained by [Bekzodbek Murotboyev!](https://www.linkedin.com/in/bekzodbek-murotboyev/)*

*Connect on [GitHub](https://github.com/bekzod-murotboyev)*

*Connect on [Linkedin](https://www.linkedin.com/in/bekzodbek-murotboyev)*

*Feel free to reach out, ask questions, or provide feedback.*