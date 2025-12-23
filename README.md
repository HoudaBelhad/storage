# Storage

[![Lines-of-Code](https://tokei.rs/b1/github/ilyalisov/storage)](https://github.com/ilyalisov/storage)
[![Hits-of-Code](https://hitsofcode.com/github/ilyalisov/storage?branch=master)](https://hitsofcode.com/github/ilyalisov/storage/view?branch=master)
[![mvn](https://github.com/ilyalisov/storage/actions/workflows/maven-build.yml/badge.svg)](https://github.com/ilyalisov/storage/actions/workflows/maven-build.yml)
[![codecov](https://codecov.io/gh/IlyaLisov/storage/graph/badge.svg?token=OJR6TFQ2qr)](https://codecov.io/gh/IlyaLisov/storage)

**Maven Build Code Coverage License: MIT Storage Maven Central**

A comprehensive Java storage library for fast and convenient storing and accessing data in your Java applications. Currently supports MinIO and Firebase storage services with a unified API.

## Table of Contents

- [Features](#features)
- [Quick Start](#quick-start)
  - [Installation](#installation)
    - [Maven](#maven)
    - [Gradle](#gradle)
- [Core Components](#core-components)
  - [Storage Service Interface](#storage-service-interface)
  - [Storage File](#storage-file)
  - [Page Pagination](#page-pagination)
- [Storage Operations](#storage-operations)
- [Supported Providers](#supported-providers)
  - [MinIO Storage Service](#minio-storage-service)
  - [Firebase Storage Service](#firebase-storage-service)
- [Usage Examples](#usage-examples)
  - [Instantiate a Service](#instantiate-a-service)
  - [Save File](#save-file)
  - [Delete File](#delete-file)
  - [Check If File Exists](#check-if-file-exists)
  - [Get File](#get-file)
- [License](#license)
- [Contributing](#contributing)

## Features

- **Unified Storage API** - Consistent interface for multiple storage providers
- **Multi-Provider Support** - Currently supports MinIO and Firebase with extensible architecture
- **File Management** - Complete file operations: save, delete, find, list
- **Pagination Support** - Built-in pagination for listing files in directories
- **Flexible Path Handling** - Support for file operations with and without path prefixes
- **Stream-Based Operations** - Efficient memory usage with InputStream-based file handling
- **Type Safety** - Strongly typed file metadata and operations

## Quick Start

Get your file storage operations up and running quickly with a unified API that works across multiple storage providers.

### Installation

#### Maven

Add to your `pom.xml`:

```xml
<dependency>
    <groupId>io.github.ilyalisov</groupId>
    <artifactId>storage</artifactId>
    <version>0.1.0</version>
</dependency>
```

#### Gradle

Add to your `build.gradle` or `build.gradle.kts`:

```kotlin
implementation("io.github.ilyalisov:storage:0.1.0")
```

## Core Components

### Storage Service Interface

The foundation for all storage operations with a unified API:

```java
public interface StorageService {
    Path save(StorageFile file);
    Path save(StorageFile file, Path path);
    void delete(String fileName);
    void delete(String fileName, Path path);
    void delete(Path path);
    boolean exists(String fileName);
    boolean exists(String fileName, Path path);
    Optional<StorageFile> find(String fileName);
    Optional<StorageFile> find(String fileName, Path path);
    List<StorageFile> findAll(Path path, Page page);
}
```

### Storage File

Represents a file with metadata and content:

```java
public class StorageFile {
    private String fileName;
    private String contentType;
    private InputStream inputStream;
    private long size;
    // ... getters, setters, and validation
}
```

### Page Pagination

Simple pagination support for listing files:

```java
public class Page {
    private int pageNumber;
    private int pageSize;
    // ... getters, setters, and validation
}
```

## Storage Operations

| Method | Description | Parameters | Returns |
|--------|-------------|------------|---------|
| `save(StorageFile)` | Save file to default location | `file` - File to save | `Path` - Path to saved file |
| `save(StorageFile, Path)` | Save file to specific path | `file` - File to save<br>`path` - Target directory | `Path` - Path to saved file |
| `delete(String)` | Delete file by name from default location | `fileName` - Name of file to delete | `void` |
| `delete(String, Path)` | Delete file by name from specific path | `fileName` - Name of file to delete<br>`path` - Directory path | `void` |
| `delete(Path)` | Delete entire directory | `path` - Directory to delete | `void` |
| `exists(String)` | Check if file exists in default location | `fileName` - Name of file to check | `boolean` - True if file exists |
| `exists(String, Path)` | Check if file exists in specific path | `fileName` - Name of file to check<br>`path` - Directory path | `boolean` - True if file exists |
| `find(String)` | Find file by name in default location | `fileName` - Name of file to find | `Optional<StorageFile>` - File if found |
| `find(String, Path)` | Find file by name in specific path | `fileName` - Name of file to find<br>`path` - Directory path | `Optional<StorageFile>` - File if found |
| `findAll(Path, Page)` | List all files in directory with pagination | `path` - Directory to list<br>`page` - Pagination parameters | `List<StorageFile>` - List of files |

## Supported Providers

### MinIO Storage Service

MinIO implementation for high-performance object storage:

```java
StorageService storageService = new MinIOStorageServiceImpl(
    "http://localhost:9000",  // host
    "rootUser",               // username
    "rootPassword",           // password
    "bucket"                  // bucket name
);
```

### Firebase Storage Service

Firebase Cloud Storage implementation:

```java
InputStream credentials = new ByteArrayInputStream(
    System.getenv("FIREBASE_SECRET").getBytes()
);
StorageService storageService = new FirebaseStorageServiceImpl(
    credentials,              // Firebase service account credentials
    "firebase-bucket"        // Firebase storage bucket
);
```

## Usage Examples

### Instantiate a Service

```java
public class Main {
    public static void main(String[] args) {
        // MinIO example
        StorageService minioService = new MinIOStorageServiceImpl(
            "http://localhost:9000",
            "rootUser",
            "rootPassword",
            "bucket"
        );
        
        // Firebase example
        InputStream firebaseCredentials = new ByteArrayInputStream(
            System.getenv("FIREBASE_SECRET").getBytes()
        );
        StorageService firebaseService = new FirebaseStorageServiceImpl(
            firebaseCredentials,
            System.getenv("FIREBASE_BUCKET")
        );
    }
}
```

### Save File

Save files with automatic path generation:

```java
public class Main {
    public static void main(String[] args) {
        StorageFile file = new StorageFile(
            "document.txt",
            "text/plain",
            new ByteArrayInputStream("File content".getBytes())
        );
        
        // Save to default location
        Path savedPath = storageService.save(file);
        
        // Save to specific folder
        Path savedInFolder = storageService.save(file, Path.of("documents"));
    }
}
```

### Delete File

Flexible file deletion operations:

```java
public class Main {
    public static void main(String[] args) {
        // Delete from default location
        storageService.delete("file.txt");
        
        // Delete from specific folder
        storageService.delete("file.txt", Path.of("folder"));
        
        // Delete entire folder
        storageService.delete(Path.of("folder"));
    }
}
```

### Check If File Exists

Verify file existence before operations:

```java
public class Main {
    public static void main(String[] args) {
        boolean existsInRoot = storageService.exists("file.txt");
        boolean existsInFolder = storageService.exists("file.txt", Path.of("folder"));
    }
}
```

### Get File

Retrieve files with optional path specification:

```java
public class Main {
    public static void main(String[] args) {
        // Find single file
        Optional<StorageFile> file = storageService.find("file.txt");
        Optional<StorageFile> fileInFolder = storageService.find("file.txt", Path.of("folder"));
        
        // List files in directory with pagination
        List<StorageFile> files = storageService.findAll(
            Path.of("folder"),
            new Page(0, 10)
        );
    }
}
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

We welcome contributions! Please feel free to submit issues and enhancement requests.

To contribute, make a fork and open a pull request. You can find issues [here](https://github.com/ilyalisov/storage/issues).

Make sure you follow the project's code style.
