# 環境変数のセットアップ
private_lane :setup_env_in_util do |options|

    # fastlane
    ENV["FASTLANE_USER"] = "datvt@wakumo.vn"
    ENV["FASTLANE_PASSWORD"] = "Thanhdat@1"
  
    # fastlane match
    ENV["MATCH_PASSWORD"] = "99"
  
    # 証明書
    ENV["CERT_DEVELOPER_ID"] = "iPhone Developer: XXXXXXX (XXXXXXXXXX)"
  
    # deploygateから生成
    ENV["DEPLOYGATE_API_TOKEN"] = "e124f2fc07137e9088168eca83e748340ab79cfa"
  
    ENV["SLACK_URL"] = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

    ENV["KEYSTORE_ENCRYPT_SECRET_KEY"] = "1FEED8DCFF3F1957541C517F4B0C8056A566A93E19136B9FA6FC6B9C093FC065"

  end
  
  # 開発の証明書の取得
  private_lane :certificate_development_in_util do |options|
  
    match(
      app_identifier: options[:app_identifiers],
      type: "development",
      force: options[:force]
    )
  end
  
  # Code Signing Style を Manual に変更
  private_lane :change_manual_code_signing_style_in_util do |options|
  
    code_sign_identity = ""
    profile_name_prefix = ""
  
    if options[:mode] == "development"
      code_sign_identity = ENV["CERT_DEVELOPER_ID"]
      profile_name_prefix = "match Development"
    end
  
    options[:app_configs].each { |app_config|
      disable_automatic_code_signing(
        targets: app_config[:target],
        code_sign_identity: code_sign_identity,
        profile_name: "#{profile_name_prefix} #{app_config[:profile_name_app_id]}"
      )
    }
  end
  
  # Flutter App をビルドする
  private_lane :build_flutter_in_util do |options|
  
    codesign = options[:platform] == "ios" ? "--no-codesign" : ""
    platform = options[:platform] == "appbundle" ? "--target-platform android-arm,android-arm64" : ""
  
    if is_ci?
      sh("cd ../../ && $FLUTTER_HOME/bin/flutter build #{options[:platform]} --release --flavor #{options[:flavor]} --target #{options[:target]} #{codesign} #{platform} -v")
    else
      sh("cd ../../ && flutter build #{options[:platform]} --release --flavor #{options[:flavor]} --target #{options[:target]} #{codesign} #{platform} -v")
    end
  end
  
  # ./buildsフォルダを作るシェル
  private_lane :scripts_mkdir_in_util do |options|
  
    sh("mkdir -p builds")
    "./fastlane/builds/"
  end
  
  # deploygate に配信する
  private_lane :deploygate_in_util do |options|
  
    deploygate(
      api_token: ENV["DEPLOYGATE_API_TOKEN"],
      user: 'datvtwkm',
      apk: options[:apk],
      release_note: options[:release_note],
      message: "[#{last_git_commit[:abbreviated_commit_hash]}] - #{last_git_commit[:message]}",
    )
  end
  # download keystore and encoce
  private_lane :fetch_keystore do |options|
    sh("rm -rf keystore-encrypted");
    sh("git clone https://github.com/datvtwkm/keystore-encrypted.git");
    sh("openssl aes-256-cbc  -d -in keystore-encrypted/keystore-encrypted -k #{ENV['KEYSTORE_ENCRYPT_SECRET_KEY']}  >> keystore.jks")
    sh("mv keystore-encrypted/keystore.jks ../..")
    sh("rm -rf keystore-encrypted")
    # sh("git clone https://github.com/datvtwkm/keystore-encrypted.git")
    # sh("cd ./keystore-encrypted && ls")
    # sh("openssl aes-256-cbc  -d -in keystore-encrypted/keystore-encrypted -k #{ENV['KEYSTORE_ENCRYPT_SECRET_KEY']}  >> keystore.jks")
    # sh("mv keystore.jks ../")
    # sh("cd .. && ls")
  end