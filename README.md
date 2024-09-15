# longriff

## Crate for doing IO on LONGRIFF-formatted files

This crate provides utility methods for reading and writing in the LONGRIFF format,
which is RIFF but everywhere we use 4 bytes we use 8 bytes instead.

Forked from [frabert/riff](https://github.com/frabert/riff).

### Examples

Reading chunks:

````rust
let mut file = File::open("somefile.longriff")?;
let chunk = longriff::Chunk::read(&mut file, 0)?;

for child in chunk.iter(&mut file) {
  println!(child.id());
}
````

Writing chunks:

````rust
// Some ids to use while creating chunks
let smpl_id: ChunkId = ChunkId { value: [0x73, 0x6D, 0x70, 0x6C, 0x20, 0x20, 0x20, 0x20] };
let test_id: ChunkId = ChunkId { value: [0x74, 0x65, 0x73, 0x74, 0x20, 0x20, 0x20, 0x20] };
let tst1_id: ChunkId = ChunkId { value: [0x74, 0x73, 0x74, 0x31, 0x20, 0x20, 0x20, 0x20] };
let tst2_id: ChunkId = ChunkId { value: [0x74, 0x73, 0x74, 0x32, 0x20, 0x20, 0x20, 0x20] };

let str1 = "hey this is a test".as_bytes().to_vec();
let str2 = "hey this is another test".as_bytes().to_vec();
let str3 = "final test".as_bytes().to_vec();

let contents = ChunkContents::Children(
  riff::RIFF_ID,
  smpl_id,
  vec![
    ChunkContents::Children(
      riff::LIST_ID,
      tst1_id,
      vec![
        ChunkContents::Data(test_id, str1),
        ChunkContents::Data(test_id, str2)
      ]),
    ChunkContents::Children(
      riff::LIST_ID,
      tst2_id,
      vec![
        ChunkContents::Data(test_id, str3)
      ]
    )
  ]);

let mut file = File::create("somefile.longriff")?;
contents.write(&mut file)?;
````
