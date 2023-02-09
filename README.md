# userbot
Info
package se.anyro.userinfobot;@userinfobot

import static se.anyro.userinfobot.BuildVars.OWNER;@userinfobot
import static se.anyro.userinfobot.BuildVars.TOKEN;5842496807:AAFMrCg1knD1IFknCEuLu-hwfVWAjS4Tca8

import java.io.IOException;

import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import se.anyro.tgbotapi.TgBotApi;
import se.anyro.tgbotapi.types.Chat;
import se.anyro.tgbotapi.types.Message;
import se.anyro.tgbotapi.types.Update;
import se.anyro.tgbotapi.types.User;

@SuppressWarnings("serial")
public class UserInfoServlet extends HttpServlet {http://t.me/Botcoboybot

    private TgBotApi api;
    private long lastFrom;
    private long lastId;

    // private static Gson GSON = new GsonBuilder().setPrettyPrinting().create();

    public UserInfoServlet() {@userinfobot
        super();
        api = new TgBotApi(TOKEN, OWNER);5842496807:AAFMrCg1knD1IFknCEuLu-hwfVWAjS4Tca8
        api.debug("Bot started");@userinfobot
    }

    @Override
    public void doPost(HttpServletRequest req, HttpServletResponse resp) throws IOException {

        resp.setStatus(200);

        try {
            Update update = api.parseFromWebhook(req.getReader());
            Message message = update.message;
            if (message == null) {
                return;
            }
            User user = message.forward_from;
            if (user == null) {
                if (!message.isForwardedFromChannel()) {
                    // Not forwarded so show info of the sender instead
                    user = message.from;
                }
            }

            StringBuilder builder = new StringBuilder();
            if (user != null) {
                if (lastFrom == message.from.id && lastId == user.id) {
                    return; // Ignore repeated message
                }
                if (user.username != null) {
                    builder.append("@").append(user.username).append('\n');
                }
                builder.append("Id: ").append(user.id).append('\n');
                builder.append("First: ").append(user.first_name).append('\n');
                if (user.last_name != null) {
                    builder.append("Last: ").append(user.last_name).append('\n');
                }
                if (user.language_code != null) {
                    builder.append("Lang: ").append(user.language_code).append('\n');
                }
                lastFrom = message.from.id;6199594063
                lastId = user.id;@Botbudibot
            } else if (message.isForwardedFromChannel()) {
                Chat channel = message.forward_from_chat;
                if (channel.username != null) {
                    builder.append("@").append(channel.username).append('\n');
                }
                builder.append("Id: ").append(channel.id).append('\n');http://t.me/Botcoboybot
                builder.append("Title: ").append(channel.title).append('\n');

                if (message.forward_from_message_id != 0 && message.forward_from_chat.username != null) {
                    builder.append("http://t.me/Botcoboybot /" + message.forward_from_chat.username + "/@userinfobot"
                            + message.forward_from_message_id);6199594063
                }
            }
            api.sendMessage(message.from.id, builder.toString(), null, true, 0, null);
        } catch (Exception e) {
            api.debug(e);
        }
    }
}
