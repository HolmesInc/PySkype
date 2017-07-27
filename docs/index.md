<p>To develop you own Skype bot, you need to create bot on Microsoft Bots Framework. So, here is the guidline, step-by-step:</p>
<ol>
  <li>Open up <a href="https://dev.botframework.com/bots">this page</a>. Sign in into you personal Microsoft account or create it.
    Than you'll see something, like this:
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/log_in_microsoft.jpg"></li>
  <li>On the opened page press "Create a bot" and than "Register"
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/bots_panel.jpg"></li>
  <li>Enter data into required fields and generate Bot app ID and password, by clicking on "Create Microsoft App ID and password (REMEMBER: You have to save password, which was generated)</li>
  <li>After that, click "Register". If everithing correct, you'll see tool tip, which infrom you, that bot was successfully created and you'll be redirected to bot setting page:
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/create_bot.jpg">
  </li>
</ol>
Now, you need to create backend for you bot. For this example we'll use Flask + PySkype.
<ol>
  <li>Start the new project. Create virtualenvironment and activate it:
    <pre>
      $ virtualenv -p python3.5 venv
      $ source venv/bin/activate
    </pre>
  </li>
  <li>Install Flask, PySkype('requests' must be installed automatically with PySkype):
    <pre>
      $ pip install flask
      $ pip install PySkype
    </pre>
  </li>
  <li>Create base file for Flask application:
    <pre>
      from flask import Flask, request
      BOT_HOST = '127.0.0.1'
      BOT_PORT = 8080
      app = Flask('WorkBot')
      @app.route('/')
      def hello():
        return "Hello World!"
      if __name__ == '__main__':
        app.run(host=BOT_HOST, port=BOT_PORT, debug=True)
    </pre>
    And run app, to test, is everithing works well.
  </li>
  <li>From PySkype import SkypeBot. Create an object and pass client id, password from Bot Framework and path to current directory, as parameters:
    <pre>
      CURRENT_DIR = os.path.dirname(os.path.abspath(__file__))
      CLIENT_ID = 'You loong id from Bot Framework, that you created previously'
      CLIENT_PASSWORD = 'Super secret password, that you created previously'
      bot = SkypeBot(CLIENT_ID, CLIENT_PASSWORD, CURRENT_DIR)
    </pre>
  </li>
  <li>Also add endpoint to receiving messages from Microsoft:
    <pre>
      @app.route('/api/messages', methods=['POST'])
      def messages():
        if request.method == 'POST':
          print('post request received')
        else:
          print('not post request')
        return 'Bot is working'
    </pre>
  </li>
  <li>Now time to use ngrok to create HTTPS tonnel for receiving messages from Microsoft. <a href="https://ngrok.com/download">Download it</a>, unzip and run:
    <pre>$ ./ngrok http 8080</pre>
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/ngrok_run.jpg">
    Select https url.
  </li>
  <li>Open up <a href="https://dev.botframework.com/bots/">list of you bots</a>, select you bot and swich to 'SETTINGS':
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/bot_settings.jpg">
  </li>  
  <li>Paste you ngrok https url to 'Messaging endpoint' input (with you Flask endpoint) and press 'Save changes':
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/paste_url.jpg">
  </li>  
  <li>Get URL for adding you bot to the contacts. Switch to 'CHANNELS' and tap on lable 'Skype'. Click 'Add to Contacts' and you bot will be added to your contacts. *NOTICE: for users, that used OS Linux I reccomend copy this URL and add you bot on your smartphone, because Skype for Linux now not supporting adding bots.</li>  
  <li>Run you app and send something to you bot. If everithing OK, you'll see request in the console and on ngrok window
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/ngrok_request.jpg">
    <img src="https://raw.githubusercontent.com/HolmesInc/PySkype/master/docs/flask_request.jpg">
  </li>
  <li>For receiving some messages from you bot, you need to get conversation id and service url from received request and use send_message method of bot object:
    <pre>
      data = json.loads(request.data.decode('utf8'))
      sender = data['conversation']['id']
      service = data['serviceUrl']
      bot.send_message(service, sender, "YEAH, I'm working!")
    </pre>
  </li>
  <li>And.. that's all=) All sources you can find by <a href="https://github.com/HolmesInc/PySkypeExample">this URL</a>. To get details power of PySkype, read README. And good luck!</li>
</ol>


