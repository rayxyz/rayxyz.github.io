---
layout: post
title: "Build Protobuf v3.0.0 on Ubuntu Linux"
date: 2017-06-16
categories: tech
---

![](https://raywangblog.files.wordpress.com/2017/06/build-protobuf-in-linux.png?w=1100?w=300)

# Caution: the autogen.sh
![modify-gmock-download-address](https://raywangblog.files.wordpress.com/2017/06/modify-gmock-download-address.png?w=1100?w=300)

```
curl $curlopts -L -O https://github.com/google/googlemock/archive/release-1.7.0.zip
unzip -q release-1.7.0.zip
rm release-1.7.0.zip
mv googlemock-release-1.7.0 gmock

curl $curlopts -L -O https://github.com/google/googletest/archive/release-1.7.0.zip
unzip -q release-1.7.0.zip
rm release-1.7.0.zip
mv googletest-release-1.7.0 gmock/gtest
```

You have to comment the original gmock-1.7.0.zip download address, it is not available anymore. Add the orange color code block. Now, you can download the zip file.

After build, check the build:

![make-check-protoc-success-result](https://raywangblog.files.wordpress.com/2017/06/make-check-protoc-success-result.png?w=1100?w=300)

Now, everything seems going well. Let’s check it out with the command proto –version:

![protoc-version-no-shared-libs](https://raywangblog.files.wordpress.com/2017/06/protoc-version-no-shared-libs.png?w=1100?w=300)

We have successfully built the Protobuf from scratch.

# References

[https://github.com/linux-on-ibm-z/docs/wiki/Building-ProtoBuf](https://github.com/linux-on-ibm-z/docs/wiki/Building-ProtoBuf)

[https://github.com/google/protobuf/commit/bba446bbf2ac7b0b9923d4eb07d5acd0665a8cf0
](https://github.com/google/protobuf/commit/bba446bbf2ac7b0b9923d4eb07d5acd0665a8cf0
)