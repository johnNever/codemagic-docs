---
description:
  Run Flutter Driver tests on physical Android and iOS devices.
title: Testing on AWS Device Farm
weight: 5
---

You can run Flutter integration tests on physical Android and iOS devices on [AWS Device Farm](https://aws.amazon.com/device-farm/) thanks to the integration with [Sylph](https://github.com/mmcc007/sylph). 

{{<notebox>}}
Using AWS Device Farm requires having an AWS account. You are billed separately by AWS for device usage. See the AWS Device Farm [pricing page](https://aws.amazon.com/device-farm/pricing/) for more information.
{{</notebox>}}

## Requirements

* An IAM user with Device Farm permissions is required. Follow the instructions outlined in the [setup document](https://docs.aws.amazon.com/devicefarm/latest/developerguide/setting-up.html) to create an IAM user with access to Device Farm and make note of the **access key ID** and **secret access key**.
* `sylph.yaml` file in the repository for AWS Device Farm configuration (optional). The `sylph.yaml` templates are available on [GitHub](https://github.com/mmcc007/sylph/tree/master/example).

{{<notebox>}}
We strongly advise you to set up an IAM user with a policy to access Device Farm and no other AWS permissions. Don't use your AWS root account credentials to link Codemagic with Device Farm.
{{</notebox>}}

## AWS Device Farm setup on Codemagic

1. Navigate to **App settings** > **Test**.
2. At the top of the section, select **Enable Flutter Driver tests** to enable running integration tests for this workflow.
3. Select to run Flutter Driver tests on **AWS Device Farm**. 
4. Enter your **AWS access key ID** and **AWS secret access Key** to the respective fields.
5. Click **Save** to finish the setup.

For testing on **iOS devices**, additional configuration is needed. Follow the instructions in the [iOS builds](https://github.com/mmcc007/sylph#ios-builds) section in Sylph readme and add the required environment variables to Codemagic in **App settings** > **Environment variables**.

During the build, Codemagic searches the repository for a `sylph.yaml` file to configure the AWS Device Farm test run and generates a default configuration file if no existing configuration is found. By default, we run the tests in your `project_root/test_driver` folder on Google Pixel 2 (OS 8.0.0) regardless of the build platforms you have selected. 

## Test logs

After the test run, you will see an overview of how many tests failed, passed or were skipped on the **Results** tab as well as a detailed test log containing information about used device minutes on the **Log** tab.

{{< figure size="medium" src="/uploads/aws_log.png" caption="" >}}

## Changing the Flutter version used for testing

Sylph comes with a predefined Flutter version set [here](https://github.com/mmcc007/sylph/blob/master/lib/resources/test_spec.yaml#L32) and doesn't yet support version changing.

If you want to test your application with another Flutter version, follow these steps:

1. Fork the [Sylph repository](https://github.com/mmcc007/sylph) and change the Flutter version in `test_spec.yaml` to the desired one. Note that the version name is followed with the channel name.
2. In Codemagic, add a pre-test script to activate your fork. 

    If you made your changes to the forked repository on the default branch, you can simply mention the git URL to activate it:

    ```
    pub global activate -s git https://github.com/your_username/sylph.git
    ```

    If you made your changes on a specific branch e.g. `flutter_1.17.5`, you can clone the specific branch and specify the path to the cloned repository:

    ```
    git clone --branch your_branch_name https://github.com/your_username/sylph.git $HOME/sylph
    pub global activate -s path $HOME/sylph
    ```