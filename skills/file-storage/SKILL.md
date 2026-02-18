---
name: File Storage
description: File Storage - upload/download, cloud storage (S3), image processing, CDN
---

# File Storage (Xử lý File)

## Tổng quan

Skill kit này hướng dẫn xử lý file: upload, storage, image processing, và CDN.

---

## Checklist

- [ ] Chọn storage strategy (local / cloud)
- [ ] Setup file upload middleware (Multer)
- [ ] File validation (type, size)
- [ ] Unique filename generation
- [ ] Image processing (resize, thumbnail)
- [ ] Setup cloud storage (S3 / Cloudinary)
- [ ] CDN cho static files
- [ ] Cleanup unused files

---

## Hướng dẫn chi tiết

### Upload với Multer (Node.js)

```typescript
import multer from 'multer';
import path from 'path';
import { v4 as uuid } from 'uuid';

// Config
const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    const ext = path.extname(file.originalname);
    cb(null, `${uuid()}${ext}`);
  },
});

const upload = multer({
  storage,
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter: (req, file, cb) => {
    const allowed = ['image/jpeg', 'image/png', 'image/webp', 'application/pdf'];
    if (allowed.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('File type not allowed'));
    }
  },
});

// Route
router.post('/upload', authenticate, upload.single('file'), async (req, res) => {
  res.json({
    success: true,
    data: {
      filename: req.file.filename,
      url: `/uploads/${req.file.filename}`,
      size: req.file.size,
    },
  });
});
```

### AWS S3 Upload

```typescript
import { S3Client, PutObjectCommand } from '@aws-sdk/client-s3';

const s3 = new S3Client({
  region: process.env.AWS_REGION,
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID!,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY!,
  },
});

const uploadToS3 = async (file: Express.Multer.File): Promise<string> => {
  const key = `uploads/${uuid()}${path.extname(file.originalname)}`;

  await s3.send(new PutObjectCommand({
    Bucket: process.env.AWS_S3_BUCKET!,
    Key: key,
    Body: file.buffer,
    ContentType: file.mimetype,
  }));

  return `https://${process.env.AWS_S3_BUCKET}.s3.amazonaws.com/${key}`;
};
```

### Image Processing (Sharp)

```typescript
import sharp from 'sharp';

const processImage = async (buffer: Buffer) => {
  // Resize + optimize
  const processed = await sharp(buffer)
    .resize(800, 600, { fit: 'inside', withoutEnlargement: true })
    .webp({ quality: 80 })
    .toBuffer();

  // Create thumbnail
  const thumbnail = await sharp(buffer)
    .resize(200, 200, { fit: 'cover' })
    .webp({ quality: 60 })
    .toBuffer();

  return { processed, thumbnail };
};
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| Không validate file type | Whitelist allowed MIME types |
| Không giới hạn file size | Set max size (5-10MB) |
| Dùng original filename | Generate unique filename (UUID) |
| Store files trong DB | Store URL/path trong DB, file trên disk/S3 |
| Không cleanup files | Delete files khi record bị xóa |
| Serve files qua app server | Dùng CDN hoặc static file server |

---

## Liên kết

- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [security](../security/SKILL.md)
