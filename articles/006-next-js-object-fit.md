      <figure className='w-80 h-60 relative'>
        <Image
          src={image.src}
          alt={image.alt}
          fill
          style={{
            objectFit: 'cover',
          }}
        />
        <figcaption className='font-mincho font-semibold leading-6 min-h-[4.5rem]'>
          {description}
        </figcaption>
      </figure>
