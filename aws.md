Tools
---
- [Amazon Web Services — a practical guide](https://github.com/open-guides/og-aws)
- [AWS SDK for Python](https://github.com/boto/boto3)


Development
---
- [A fully functional local AWS cloud stack. Develop and test your cloud apps offline! ](https://github.com/localstack/localstack)
```
pyenv versions
pyenv virtualenv 2.7.13 localstack-virtual-env
pyenv activate localstack-virtual-env
pip install localstack
aws --endpoint-url=http://localhost:4568 kinesis list-streams
pip install awscli-local
localstack web
```

configuration management
---
- [aws chef cookbooks](https://github.com/chef-cookbooks/aws)
