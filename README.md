
format_version: "11"
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git
project_type: react-native

workflows:
  primary:
    steps:
      - git-clone@8: {}

      - npm@3:
          inputs:
            - command: install

      - script@1:
          title: Build Turbo Metz APK
          inputs:
            - content: |
                #!/usr/bin/env bash
                set -e

                echo "=== Generando proyecto Android ==="
                npx expo prebuild --platform android --non-interactive

                echo "=== Compilando APK ==="
                cd android
                chmod +x gradlew
                ./gradlew assembleDebug

                echo "=== Preparando APK ==="
                mkdir -p "$BITRISE_DEPLOY_DIR"
                cp app/build/outputs/apk/debug/app-debug.apk \
                  "$BITRISE_DEPLOY_DIR/Turbo-Metz.apk"

                echo "=== APK preparada ==="

      - deploy-to-bitrise-io@2: {}
