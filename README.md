# Kbot

A functional Telegram bot with root command and settings. It processes messages from user: in response for "/start hello", kbot returns "Hello I'm Kbot <version>!", in all other cases - "Sorry, I can only respond to 'hello'."

Technologies:

- Language: Go (Golang)
- Frameworks: `github.com/spf13/cobra` and `gopkg.in/telebot.v3`

The bot includes:

- Message handlers to respond to Telegram messages.
- Functions for bot message handling.
- Methods for processing messages based on their type and content.

## Link to the Bot

[t.me/igortsb_bot](https://t.me/igortsb_bot)

## Installation Instructions

Follow these steps to create a Telegram bot and deploy it from scratch:

### I. Create a Github Project, Install Golang, Set Up Codespace IDE

1. Create a new Github repository from the web UI:
   - Follow the instructions here: [Creating a new repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository)

2. Enable Codespaces environment for development:
   - Press `.` on the keyboard while viewing your repository.

3. Initialize Go module in the project:

    ```sh
    go mod init <project_name>
    # Example
    go mod init github.com/igortsb/kbot
    ```

### II. Add Dependency on `github.com/spf13/cobra` Library

4. Install Cobra CLI code generator:

    ```sh
    go install github.com/spf13/cobra-cli@latest
    ```

5. Initialize Cobra-CLI to create main code file:

    ```sh
    cobra-cli init
    ```

6. Add code for a version command to `cmd` folder:

    ```sh
    cobra-cli add version
    ```

7. Build your code for the first time:

    ```sh
    go run main.go help
    ```

8. Add command that will include the main code for the kbot app:

    ```sh
    cobra-cli add kbot
    ```

9. Add variable `appVersion` to `version.go` command:

    ```go
    var appVersion = "v1.0.0"

    var versionCmd = &cobra.Command{
        Use:   "version",
        Short: "A brief description of your command",
        Long: `A longer description that spans multiple lines and likely contains examples
    and usage of using your command. For example:

    Cobra is a CLI library for Go that empowers applications.
    This application is a tool to generate the needed files
    to quickly create a Cobra application.`,
        Run: func(cmd *cobra.Command, args []string) {
            fmt.Println(appVersion)
        },
    }
    ```

10. Compile the Go source code into an executable binary passing the version to the linker:

    ```sh
    go build -ldflags "-X=github.com/igortsb/kbot/cmd.appVersion=v1.0.0"
    ```

11. Call the `./kbot` command with version parameter:

    ```sh
    ./kbot version
    ```

### III. Create Telegram Bot using BotFather

12. Message [@BotFather](https://t.me/botfather) on Telegram to register your bot and receive its authentication token. Use the following details:
    - Bot name: `kbot`
    - Bot username: `igortsb_bot`
    - Bot token: `YOUR_BOT_TOKEN`
    - Bot URL: [t.me/igortsb_bot](https://t.me/igortsb_bot)

### IV. Import Necessary Libraries

13. Update `kbot.go` to add telebot library:

    ```go
    import (
        tele "gopkg.in/telebot.v3"
    )
    ```

14. Add `TELE_TOKEN` variable:

    ```go
    var (
        TeleToken = os.Getenv("TELE_TOKEN")
    )
    ```

15. Add `Run` function code in `kbot.go`, a bot initialization block to create a bot object via `telebot.NewBot()` and Telebot message handler via kbot.Handle(telebot.OnText, func(m telebot.Context):

    ```go
    var kbotCmd = &cobra.Command{
        ...
        Run: func(cmd *cobra.Command, args []string) {
            fmt.Printf("kbot %s started\n", appVersion)
            kbot, err := telebot.NewBot(telebot.Settings{
                URL: "",
                Token: TeleToken,
                Poller: &telebot.LongPoller{Timeout: 10 * time.Second},
            })
            if err != nil {
                log.Fatalf("Please check TELE_TOKEN env variable. %s", err)
                return
            }

           kbot.Handle(telebot.OnText, func(m telebot.Context) error {
			
			log.Print(m.Message().Payload, m.Text())
			payload := m.Message().Payload
			
			switch payload {
				case "hello":
					err = m.Send(fmt.Sprintf("Hello I'm Kbot %s!", appVersion))
				
				default:
					err = m.Send("Sorry, I can only respond to 'hello'.")
				}
			
				if err != nil {
					log.Printf("Error sending message: %s", err)
				}	
			
				return err
		})

            kbot.Start()
        },
    }
    ```

### V. Build Kbot

16. Format code according to the Go standards:

    ```sh
    gofmt -s -w ./
    ```

17. Install necessary Go packages:

    ```sh
    go get
    ```

18. Build the kbot app:

    ```sh
    go build -ldflags "-X=github.com/igortsb/kbot/cmd.appVersion=v1.0.1"
    ```

19. Add alias to the kbot command in `kbot.go`:

    ```go
    var kbotCmd = &cobra.Command{
        Use:     "kbot",
        Aliases: []string{"start"},
        ...
    }
    ```

20. Build the kbot app again:

    ```sh
    go build -ldflags "-X=github.com/igortsb/kbot/cmd.appVersion=v1.0.1"
    ```

### VI. Save Token into Environment Variable `TELE_TOKEN`

21. Save token into environment variable `TELE_TOKEN` securely:

    ```sh
    read -s TELE_TOKEN
    ```

22. Check the value of the `TELE_TOKEN`:

    ```sh
    echo $TELE_TOKEN
    ```

23. Declare the `TELE_TOKEN` environment variable:

    ```sh
    export TELE_TOKEN
    ```

### VII. Run and test Kbot

24. Start and test kbot:

    ```sh
    ./kbot start 
    ```