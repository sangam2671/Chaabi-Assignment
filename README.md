# Chaabi-Assignment
step 1: First I read the files using pandas dataframe
Step 2: Create Vector embeddings initially using Auto tokenizer and BERT but as soon as i go to Qdrant than changes it and using its own tokenizer
from qdrant_client import models, QdrantClient
from sentence_transformers import SentenceTransformer

You need to tell Qdrant where to store embeddings. This is a basic demo, so your local computer will use its memory as temporary storage.

qdrant = QdrantClient(":memory:")



All data in Qdrant is organized by collections. In this case, you are storing books, so we are calling it my_books.
qdrant.recreate_collection(
    collection_name="my_books",
    vectors_config=models.VectorParams(
        size=encoder.get_sentence_embedding_dimension(),  # Vector size is defined by used model
        distance=models.Distance.COSINE,
    ),
)



Tell the database to upload documents to the my_books collection. This will give each record an id and a payload. The payload is just the metadata from the dataset.

qdrant.upload_records(
    collection_name="my_books",
    records=[
        models.Record(
            id=idx, vector=encoder.encode(doc["description"]).tolist(), payload=doc
        )
        for idx, doc in enumerate(documents)
    ],
)


Now that the data is stored in Qdrant, you can ask it questions and receive semantically relevant results.

hits = qdrant.search(
    collection_name="my_books",
    query_vector=encoder.encode("alien invasion").tolist(),
    limit=3,
)
for hit in hits:
    print(hit.payload, "score:", hit.score)


Finally pre trained on BERT QUESTION Answering and find out the results.
