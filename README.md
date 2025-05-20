# Moodle Selenium Grid

This repository contains a Docker Compose setup for running a Selenium Grid with Chrome, Firefox, and Edge browsers for testing Moodle applications.

All the necessary configurations are included to get you started quickly without any additional setup such as installing browser drivers or configuring the Selenium Grid manually.

## Setup instructions

1. Install Docker and Docker Compose on your machine.
2. Clone this repository to your local machine.
3. Navigate to the cloned repository directory.
4. Run the following command to start the Selenium Grid and Browsers containers:

    If you are using **AMD64** architecture:

    ```bash
    docker compose pull
    docker compose up -d
    ```

    If you are using **macOS** with **Apple Silicon chip** or **Linux/ARM**:

    ```bash
    docker compose -f docker-compose-arm.yml up -d
    ```

## Usage instructions

1. Set up the test host e.g. 'behat.test' in your `/etc/hosts` file:

   ```bash
   127.0.0.1 localhost behat.test
   ```

2. Open your Moodle `config.php` in your preferred editor.
3. Add the following lines to the `config.php` file:

    ```php
    $CFG->behat_wwwroot = 'http://behat.test';
    $CFG->behat_profiles = [
        'chrome' => [
            'browser' => 'chrome',
            'capabilities' => [
                'extra_capabilities' => [
                    'goog:chromeOptions' => [
                        'args' => [
                            'no-sandbox',
                            'no-gpu',
                            'disable-save-password-bubble',
                        ],
                        'prefs' => [
                            // Disable the Credential service.
                            'credentials_enable_service' => false,
                            // Disable the Password manager.
                            'profile.password_manager_enabled' => false,
                            'profile.password_manager_leak_detection' => false,
                        ],
                    ],
                ],
            ],
            'wd_host' => 'http://localhost:4444/wd/hub',
        ],
        'firefox' => [
            'browser' => 'firefox',
            'capabilities' => [
                'marionette' => true,
            ],
            'wd_host' => 'http://localhost:4444/wd/hub',
        ],
        'edge' => [
            'browser' => 'edge',
            'capabilities' => [
                'marionette' => true,
            ],
            'wd_host' => 'http://localhost:4444/wd/hub',
        ],
    ];
   ```

   Or you can use Andrew's [Moodle Behat Browser configuration utility](https://github.com/andrewnicols/moodle-browser-config) to generate the browser configuration for you.
4. Run behat, specifying one of the defined browsers as a profile, for example:

    ```bash
    vendor/bin/behat --profile=chrome --config=/moodledata_behat/behatrun/behat/behat.yml --tags=@core_ai
    ```

5. The supported profiles are:
   - `chrome` for latest Google Chrome
   - `firefox` for latest Mozilla Firefox
   - `edge` for latest Microsoft Edge (Except for **macOS** with **Apple Silicon chip** and **Linux/ARM**)

## Using VNC to view behat tests

1. Download a VNC viewer application (e.g., RealVNC, TightVNC, etc.) on your local machine.
2. With the containers running, enter the following information in your VNC viewer:
   - **VNC Server**: `localhost:5900` for Chrome, `localhost:5901` for Firefox, and `localhost:5902` for Edge.
   - **Password**: `secret`
3. You should be able to see an empty Desktop. When you run any [Javascript requiring Behat tests](https://moodledev.io/general/development/tools/behat#javascript) (e.g. those tagged @javascript) a browser will popup and you will see the tests execute.
