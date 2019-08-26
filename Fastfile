private_lane :setup_env_in_util do |options|

    ENV["MATCH_PASSWORD"] = "99"
  
    ENV["CERT_DEVELOPER_ID"] = "iPhone Developer: XXXXXXX (XXXXXXXXXX)"
  
    ENV["DEPLOYGATE_API_TOKEN"] = "e124f2fc07137e9088168eca83e748340ab79cfa"
  
    ENV["SLACK_URL"] = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

    ENV["KEYSTORE_ENCRYPT_SECRET_KEY"] = "1FEED8DCFF3F1957541C517F4B0C8056A566A93E19136B9FA6FC6B9C093FC065"

  end
  
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
  
  private_lane :build_flutter_in_util do |options|
    flavor = options[:flavor]
    file_type = options[:file_type]
    target = options[:target]
    version = options[:version]
    build_number = options[:build_number]

    codesign = file_type == "ipa" ? "--no-codesign" : ""
    target_platform = file_type == "appbundle" ? "--target-platform android-arm,android-arm64" : ""
  
    sh("cd ../../ && $FLUTTER_HOME/bin/flutter build #{file_type} --release --flavor #{flavor} --target #{target} #{codesign} #{target_platform} --build-name #{version} --build-number #{build_number} -v")
  end
  
  # ./buildsフォルダを作るシェル
  private_lane :scripts_mkdir_in_util do |options|
    sh("mkdir -p builds")
    "./fastlane/builds/"
  end
  
  # download encrypted and decrypt
  private_lane :fetch_and_decrypt_in_util do |options|
    git_url= options[:git_url]
    encrypted_path= options[:encrypted_path]
    decrypted_path = options[:decrypted_path]
    sh("git clone #{git_url}");
    sh("openssl aes-256-cbc  -d -in #{encrypted_path} -k #{ENV['KEYSTORE_ENCRYPT_SECRET_KEY']} -md md5  >> #{decrypted_path}")
  end

  # delete file or folder
  private_lane :delete_file_or_folder_in_util do |options|
    path= options[:path]
    sh("rm -rf #{path} || rm -R #{path} || true");
  end