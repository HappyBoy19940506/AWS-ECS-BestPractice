# AWS-ECS-BestPractice
https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/intro.html


#https://github.com/aws/aws-cdk/issues/12720

I solved this issue by following the steps below, hope that helps.

Error: "ResourceInitializationError: failed to download env files: file download command: non-empty error stream: RequestCanceled: request context cancelled caused by: context deadline exceeded

Reason: This may be because the subnets that hold your Farget tasks do not have an accessible way to visit your S3 bucket or your SSM parameter store via HTTP(s) request.
There are three ways to let Farget visit your other AWS resources like S3.

IF you put Farget in public subnets. The public subnets must be associated with an Internet Gateway and enable auto assign Public IPs.
IF you put Farget in private subnets. The public subnets must be associated with a Nat Gateway.
Or you can use the VPC endpoint to reach other AWS resources. In this way, no associated Gateway is needed.
Error : ResourceInitializationError: failed to download env files: file download command: non-empty error stream: failed to download file 1.env: failed to write to a temporary file: AccessDenied: Access Denied

If you get this AccessDenied: Access Denied error.

Reason: This is usually due to permission problems.
Go Check the Required IAM permissions part to modify the ecsTaskExecutionRole.

Other weird things: As the doc said, You need to attach an inline_policie to the ecsTaskExecutionRole IAM rather than create a specific another Task _Role for your tasks, which means task_role, as well as task_execution_role, meant to be exactly the same one. But if I separate them apart, like Task Role for only s3 read policy, Task execution role for only default AmazonECSTaskExecutionRolePolicy. It will lead to Access Denied problem.
Not quite sure if it's a bug or if I misunderstand something. Apparently, the task role is designed for passing env into a container. But not only permission to visit s3/SSM, but the original AmazonECSTaskExecutionRolePolicy is also needed.


