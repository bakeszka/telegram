from flask import Flask, request
import telebot
import stripe

app = Flask(__name__)

# Config
TELEGRAM_BOT_TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"
STRIPE_SECRET_KEY = "YOUR_STRIPE_SECRET_KEY"
bot = telebot.TeleBot(TELEGRAM_BOT_TOKEN)
stripe.api_key = STRIPE_SECRET_KEY

@app.route('/webhook', methods=['POST'])
def stripe_webhook():
    payload = request.get_data(as_text=True)
    sig_header = request.headers.get('Stripe-Signature')
    event = None

    try:
        event = stripe.Webhook.construct_event(
            payload, sig_header, "YOUR_ENDPOINT_SECRET"
        )
    except ValueError as e:
        return "Invalid payload", 400
    except stripe.error.SignatureVerificationError as e:
        return "Invalid signature", 400

    if event['type'] == 'checkout.session.completed':
        session = event['data']['object']
        telegram_user_id = session['metadata']['telegram_user_id']
        bot.send_message(telegram_user_id, "Köszönjük a fizetést! Itt a csoport link: https://t.me/joinchat/YOUR_GROUP_INVITE_LINK")

    return "Success", 200

if __name__ == '__main__':
    app.run(port=5000)
