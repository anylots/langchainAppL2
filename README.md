# LangchainAppL2 - Your GenerativeAI About Layer2


LangchainAppL2 is your GenerativeAI About Layer2, designed to easily store and retrieve unstructured information. It's like Obsidian but powered by generative AI. This App is based on Langchain and Quivr projects, Will continue to develop on a large scale.




## Features

- **Store Anything**: Quivr can handle almost any type of data you throw at it. Text, images, code snippets, you name it.
- **Generative AI**: Quivr uses advanced AI to help you generate and retrieve information.
- **Fast and Efficient**: Designed with speed and efficiency in mind. Quivr makes sure you can access your data as quickly as possible.
- **Secure**: Your data is always under your control.
- **Compatible Files**: 
  - **Text**
  - **Markdown**
  - **PDF**
  - **Powerpoint**
  - **Excel**
  - **Word**
  - **Audio**
  - **Video**
- **Open Source**: Quivr is open source and free to use.

## Getting Started with the new version
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.
*Old version readme is in the streamlit-demo folder* [here](streamlit-demo/README.md)

### Prerequisites

You'll also need a [Supabase](https://supabase.com/) account for:

- A new Supabase project
- Supabase Project API key
- Supabase Project URL

### Installing

- Clone the repository by running **one** of these commands:

  1. If you don't have an SSH key set up:
  ```bash
  git clone https://github.com/StanGirard/Quivr.git && cd Quivr
  ```

  2. If you have an SSH key set up or want to add it (https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

  ```bash
  git clone git@github.com:StanGirard/Quivr.git && cd Quivr
  ```

- Copy the `.XXXXX_env` files

```bash
cp .backend_env.example .backend_env
cp .frontend_env.example .frontend_env
```

- Update the `.backend_env` file 

_Note that the `supabase_service_key` is found in your Supabase dashboard under Project Settings -> API. Use the `anon` `public` key found in the `Project API keys` section._


- Run the following migration scripts on the Supabase database via the web interface (SQL Editor -> `New query`)

```sql
-- Enable the pgvector extension to work with embedding vectors
       create extension vector;

       -- Create a table to store your documents
       create table documents (
       id bigserial primary key,
       content text, -- corresponds to Document.pageContent
       metadata jsonb, -- corresponds to Document.metadata
       embedding vector(1536) -- 1536 works for OpenAI embeddings, change if needed
       );

       CREATE FUNCTION match_documents(query_embedding vector(1536), match_count int)
           RETURNS TABLE(
               id bigint,
               content text,
               metadata jsonb,
               -- we return matched vectors to enable maximal marginal relevance searches
               embedding vector(1536),
               similarity float)
           LANGUAGE plpgsql
           AS $$
           # variable_conflict use_column
       BEGIN
           RETURN query
           SELECT
               id,
               content,
               metadata,
               embedding,
               1 -(documents.embedding <=> query_embedding) AS similarity
           FROM
               documents
           ORDER BY
               documents.embedding <=> query_embedding
           LIMIT match_count;
       END;
       $$;
```

and 

```sql
create table
  stats (
    -- A column called "time" with data type "timestamp"
    time timestamp,
    -- A column called "details" with data type "text"
    chat boolean,
    embedding boolean,
    details text,
    metadata jsonb,
    -- An "integer" primary key column called "id" that is generated always as identity
    id integer primary key generated always as identity
  );
```

- Run the app

```bash
streamlit run main.py
```

## Built With

* [Python](https://www.python.org/) - The programming language used.
* [Supabase](https://supabase.io/) - The open source Firebase alternative.


