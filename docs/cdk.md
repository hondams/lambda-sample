# CDK

## 環境構築

```powershell

winget install -e --id OpenJS.NodeJS

winget install -e --id Amazon.AWSCLI
winget install -e --id Amazon.SAM-CLI

```

```cmd

npm install -g typescript
npm install -g aws-cdk

```

- vs code extenssion
  - AmazonWebServices.aws-toolkit-vscode

## ブランクプロジェクト

```cmd

cdk init app --language typescript

```

## ノウハウ

- [Getting started with the AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/getting-started.html)
- [AWS CDK prerequisites](https://docs.aws.amazon.com/cdk/v2/guide/prerequisites.html)

- [Tutorial: Create your first AWS CDK app](https://docs.aws.amazon.com/cdk/v2/guide/hello-world.html)

## CDKベストプラクティス

- Constructは、ステートレス、ステートフルで分ける。
  - ステートレスは、デプロイの単位
  - ステートフルは、永続化の単位
- Construct 間の値の受け渡しは、Parameter Storeを利用する。
  - [Systems Manager Parameter Store から値の取得](https://docs.aws.amazon.com/ja_jp/cdk/v2/guide/get-ssm-value.html)
    - `/CDK/<Environment>/<ConstructCategory>/<ResourceName>`
      - `<Environment>` : Dev,Staging,Prod
      - `<ConstructCategory>` : Network,DataSource,Application
- [Best practices for developing and deploying cloud infrastructure with the AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/best-practices.html)
  - 組織のベストプラクティス
    - 社内の従業員のトレーニングと指導を担当する専門家チームを編成する
      - 大規模企業では本格的な Cloud Center of Excellence (CCoE)
      - 社内のクラウド インフラストラクチャに関する標準とポリシーの設定、そして開発者のトレーニングと指導を担当
    - AWS内の組織単位を定義する「ランディングゾーン」も作成します。
      - ランディングゾーンとは、ベストプラクティスのブループリントに基づいて、事前設定済みの安全でスケーラブルなマルチアカウントAWS環境です。
      - ランディングゾーンを構成するサービスを連携させるには、AWS Control Towerを使用できます。
        - 単一のユーザー インターフェイスからマルチ アカウント システム全体を構成および管理します。
    - テスト用に独自のアカウントを使用し、必要に応じてこれらのアカウントに新しいリソースをデプロイできる必要があります。
      - CDK Pipelinesを使用すると、AWS CDK アプリケーションを CI/CD アカウント経由でテスト環境、統合環境、本番環境（それぞれが独自の AWS リージョンまたはアカウントに分離されています）にデプロイできます。
        - [](https://docs.aws.amazon.com/cdk/v2/guide/cdk-pipeline.html)
      - 開発者のコ​​ードを組織の正規リポジトリにマージすることで実現されます。
  - コーディングのベストプラクティス
    - シンプルに始め、必要なときだけ複雑さを追加します
      - シンプルすぎるということはありません。
      - 要件によって複雑なソリューションが必要になる場合にのみ、複雑さを増してください。
    - AWS Well-Architected フレームワークに準拠
      - コンポーネントとは、要件を満たすコード、設定、AWSリソースをまとめて提供するもの
      - ワークロードという用語は、ビジネス価値をまとめて提供するコンポーネントのセットを指す
      - AWS CDK アプリは、Well-Architected クラウドアプリケーションのベストプラクティスを体系化し、提供するためのメカニズムです。
      - AWS CodeArtifact などのアーティファクトリポジトリを通じて、再利用可能なコードライブラリとしてコンポーネントを作成および共有することもできます。
      - [](https://aws.amazon.com/architecture/well-architected/)
    - すべてのアプリケーションは、単一のリポジトリ内の単一のパッケージから始まります
      - 単一のパッケージは、AWS CDK アプリのエントリポイントです。
        - アプリケーションのさまざまな論理ユニットをどのように、どこにデプロイするかを定義します。
        - アプリの構成要素によって、ソリューションの論理ユニットが定義されます。
      - アプリケーションをデプロイするための CI/CD パイプラインも定義します。
      - 複数のアプリケーションで使用する構成要素には、追加のパッケージを使用します。
        - 同じリポジトリ内のパッケージ間の依存関係は、リポジトリのビルド ツールによって管理されます。
      - リポジトリに複数のアプリケーションがある場合、1つのアプリケーションに変更を加えると、他のアプリケーションに変更がない場合でも、他のアプリケーションのデプロイがトリガーされます。
    - コードのライフサイクルまたはチームの所有権に基づいてコードをリポジトリに移動する
      - パッケージが複数のアプリケーションで使用され始めたら、それらを専用のリポジトリに移動
        - パッケージはそれらを使用するアプリケーションビルドシステムから参照できるようになり、アプリケーションのライフサイクルとは独立した周期で更新できるようになります。
        - 最初は、共有されるすべての構成要素を1つのリポジトリに配置する方が合理的かもしれません。
      - パッケージをビルド、テストし、プライベートパッケージリポジトリに公開するリリースプロセスも必要です。
        - CodeArtifactは、ほとんどの一般的なプログラミング言語のパッケージをホストできます。
        - 共有パッケージは、一般公開されているかのように、使用するアプリケーションとは独立してテストする必要があります。
      - インフラストラクチャとランタイムコードは同じパッケージ内に存在する
        - インフラストラクチャを定義するコードとランタイムロジックを実装するコードを 1 つの構造に統合することが可能になります。
          - インフラストラクチャをデプロイするための AWS CloudFormation テンプレートを生成するだけでなく、Lambda 関数や Docker イメージなどのランタイムアセットもバンドルし、インフラストラクチャと一緒にデプロイします。
          - 2種類のコードを個別にテストし、プロジェクト間でコードを共有・再利用し、すべてのコードのバージョンを同期させることができます。
  - ベストプラクティスを構築する
    - 構造でモデル化し、スタックで展開する
      - スタックはデプロイメントの単位です。スタック内のすべての要素はまとめてデプロイされます。
      - 複数のAWSリソースからアプリケーションの上位レベルの論理ユニットを構築する場合は、各論理ユニットを スタックではなくコンストラクトとして表してください。
      - スタックは、様々なデプロイメントシナリオに合わせてコンストラクトをどのように構成し、接続するかを記述する場合にのみ使用してください。
    - 環境変数ではなくプロパティとメソッドで設定する
      - コンストラクトやスタック内での環境変数参照は、よくあるアンチパターン
        - コンストラクトとスタックはどちらも、コード内で完全に設定できるようにするため、プロパティオブジェクトを受け入れるべき
      - コードが実行されるマシンへの依存させない
      - 環境変数の参照はAWS CDKアプリのトップレベルに限定する必要があります。
        - 開発環境での実行に必要な情報を渡すためにも使用する必要があります。
    - インフラストラクチャのユニットテスト
      - すべての環境でビルド時に完全なユニットテストスイートを一貫して実行するには、合成中のネットワークルックアップを回避し、すべての本番環境段階をコードでモデル化します。
      - 単一のコミットで常に同じテンプレートが生成されれば、作成したユニットテストを信頼して、生成されたテンプレートが期待どおりであることを確認できます。
        - [](https://docs.aws.amazon.com/cdk/v2/guide/testing.html)
    - ステートフルリソースの論理IDを変更しない
      - リソースの論理 ID を変更すると、次回のデプロイ時にリソースが新しい ID に置き換えられます。
        - データベースや S3 バケットなどのステートフルリソース、または Amazon VPC などの永続インフラストラクチャ
      - ステートフルリソースの論理 ID が静的のままであることをアサートする単体テストを記述してください。
      - 論理 ID は、コンストラクトをidインスタンス化するときに指定した ID と、コンストラクトツリー内のコンストラクトの位置から派生​​します。
        - [](https://docs.aws.amazon.com/cdk/v2/guide/identifiers.html#identifiers-logical-ids)
    - コンプライアンスには構成だけでは不十分
      - サービスコントロールポリシーや権限境界などのAWS機能を使用して、組織レベルでセキュリティガードレールを強化しましょう。
        - AspectsとAWS CDK 、またはCloudFormation Guardなどのツールをご利用ください。
          - [](https://docs.aws.amazon.com/cdk/v2/guide/aspects.html)
          - [](https://github.com/aws-cloudformation/cloudformation-guard)
        - デプロイ前にインフラストラクチャ要素のセキュリティ特性を検証します。
        - 独自の「L2+」コンストラクトを作成すると、開発者がAWS CDKパッケージを利用できなくなる可能性がある
  - アプリケーションのベストプラクティス
    - 合成時に決定を下す
      - AWS CDK アプリケーション内で、どの構成要素をインスタンス化するかといったすべての決定を、プログラミング言語のifステートメントやその他の機能を使って行う
    - 物理名ではなく生成されたリソース名を使用する
      - 名前は貴重なリソースです。それぞれの名前は一度しか使用できません。
        - ハードコードした場合、同じアカウントにそのインフラストラクチャを2回デプロイすることはできません。
      - 

## AWSベストプラクティス

- [パラメータストアでのパラメータ階層の操作](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-hierarchies.html)
  - Parameter Storeのパラメータ名は、`/`で階層化する。

- [Amazon リソースネーム (ARN) で AWS リソースを識別する](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/reference-arns.html)
- [識別子と AWS CDK](https://docs.aws.amazon.com/ja_jp/cdk/v2/guide/identifiers.html)
